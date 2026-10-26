# Squad Charter: Capture

**Tech Lead:** [Name]  
**Mission:** Build the system-boundary capture engine that records every non-deterministic input to an agent execution (syscalls, network, filesystem, time, randomness) with happens-before ordering, streaming to disk with <5% overhead.

---

## OKRs (Day 5 Outcomes)

| OKR | Metric | Target |
|-----|--------|--------|
| **OKR 1: Capture Completeness** | % of non-deterministic sources captured (syscalls, net, fs, time, rand, signals) | 100% of identified sources |
| **OKR 2: Performance** | Capture overhead on benchmark agent (LangGraph 100-step run) | <5% latency overhead, <10% memory overhead |
| **OKR 3: Streaming Reliability** | Zero event loss during 1-hour continuous capture under load | 0 lost events, <100ms write latency p99 |

---

## Dependencies on Other Squads

| Need From | Squad | Required By | Details |
|-----------|-------|-------------|---------|
| Event schema (JSON) + content-addressing spec | Replay Engine | Day 1 EOD | Contract: `contracts/capture-to-log.md` |
| Happens-before edge format | Replay Engine | Day 1 EOD | Must support vector clocks or hybrid logical clocks |
| Snapshot trigger points | State | Day 2 EOD | When to emit full state snapshots vs. deltas |
| CLI flags for capture config | UI & CLI | Day 3 EOD | `--capture-syscalls`, `--capture-network`, etc. |

---

## Top 3 Risks + Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| **eBPF/ptrace too fragile on target OSes** | High | Blocker | Spike Day 1: test bpftrace, gvisor, rr on Ubuntu 22.04/24.04, macOS, WSL2; fallback to user-space LD_PRELOAD |
| **Event volume explodes on high-throughput agents** | Medium | High | Implement adaptive sampling + aggregation; content-addressing deduplication; benchmark early (Day 2) |
| **Concurrency ordering bugs (lost happens-before edges)** | Medium | Critical | Spike Day 1: verify vector clock implementation on parallel workloads; property-based test with quickcheck |

---

## Day 1–2 Spike Needed?

**YES** — **Spike: Capture Backend Selection** (Owner: Capture Tech Lead, Due: Day 2 EOD)
- Compare: `bpftrace` + `libbpf` vs `gVisor` vs `rr` vs `LD_PRELOAD` hooking
- Criteria: portability, overhead, syscall coverage, container support, maintenance burden
- Deliverable: 1-page decision doc with benchmarks → commit to `docs/capture-backend-decision.md`