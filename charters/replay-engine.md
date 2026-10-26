# Squad Charter: Replay Engine

**Tech Lead:** [Name]  
**Mission:** Build the deterministic replay engine that re-executes captured agent runs step-by-step, supports forking at any step with edited inputs, and exposes a query API for causal navigation.

---

## OKRs (Day 5 Outcomes)

| OKR | Metric | Target |
|-----|--------|--------|
| **OKR 1: Deterministic Replay** | Bit-for-bit identical output on replay vs. original run (given same inputs) | 100% match on benchmark suite |
| **OKR 2: Fork & Edit Latency** | Time to fork at step N, edit one tool result, resume to completion | <2s for 100-step benchmark |
| **OKR 3: Query API Completeness** | API supports: get run, get step, get causal ancestors, get divergent paths | All 4 endpoints implemented + tested |

---

## Dependencies on Other Squads

| Need From | Squad | Required By | Details |
|-----------|-------|-------------|---------|
| Capture event log format + streaming | Capture | Day 1 EOD | Contract: `contracts/capture-to-log.md` |
| State restore API (files, DB, network, processes) | State | Day 2 EOD | Contract: `contracts/replay-to-state.md` |
| Divergence detection hook spec | State | Day 2 EOD | Callback when replay diverges from original |
| UI event stream format (progress, diffs) | UI & CLI | Day 3 EOD | Contract: `contracts/state-to-ui.md` |

---

## Top 3 Risks + Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| **Non-determinism sources missed by capture** | Medium | Critical | Contract review Day 1: verify capture covers all sources; add "unknown source" fallback that records raw bytes |
| **Fork state explosion (copy-on-write too slow)** | High | High | Implement copy-on-write at page level; benchmark fork latency Day 2; spike on DM-COW vs. user-space |
| **Causal ordering bugs in concurrent replay** | Medium | Critical | Property-based testing with generated concurrent traces; verify vector clock merge logic Day 2 |

---

## Day 1–2 Spike Needed?

**YES** — **Spike: Fork State Management Strategy** (Owner: Replay Tech Lead, Due: Day 2 EOD)
- Compare: `dm-cow` (device-mapper) vs `overlayfs` vs user-space copy-on-write vs `gVisor` snapshots
- Criteria: fork latency, memory overhead, cross-platform (Linux/macOS/WSL), container compatibility
- Deliverable: Decision doc + microbenchmarks → `docs/fork-strategy-decision.md`