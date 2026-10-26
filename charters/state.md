# Squad Charter: State

**Tech Lead:** [Name]  
**Mission:** Build the environment state management layer — snapshot/restore of filesystem, databases, network sockets, process tree, and kernel state — with copy-on-write forks and side-effect policy enforcement.

---

## OKRs (Day 5 Outcomes)

| OKR | Metric | Target |
|-----|--------|--------|
| **OKR 1: State Coverage** | % of environment dimensions snapshotted (fs, DB, net, proc, kernel, time) | 100% of 6 dimensions |
| **OKR 2: Snapshot/Restore Speed** | Full snapshot + restore time for 1GB workspace + PostgreSQL + 50 network sockets | <3s snapshot, <2s restore |
| **OKR 3: Fork Isolation** | Forked execution sees zero side-effects from parent after fork point (verified by stress test) | 0 leakage in 1000 parallel fork stress test |

---

## Dependencies on Other Squads

| Need From | Squad | Required By | Details |
|-----------|-------|-------------|---------|
| Snapshot trigger points from replay | Replay Engine | Day 2 EOD | Contract: `contracts/replay-to-state.md` — when to snapshot (every step? checkpoints?) |
| Side-effect policy enum (allow/deny/record) | Replay Engine | Day 2 EOD | What happens when forked exec tries network call |
| State diff format for UI | UI & CLI | Day 3 EOD | Contract: `contracts/state-to-ui.md` |
| CLI commands for manual snapshot/inspect | UI & CLI | Day 4 EOD | `rewind state snapshot`, `rewind state diff` |

---

## Top 3 Risks + Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| **Database snapshot/restore too slow (PostgreSQL, Redis)** | High | High | Spike Day 1: test `pg_dump` vs `pg_basebackup` vs ZFS/btrfs snapshots vs logical replication slots; pick fastest |
| **Network socket restore impossible (TCP state)** | High | Critical | Spike Day 1: test `CRIU` (Checkpoint/Restore In Userspace) for socket restore; fallback: record/replay network at capture layer |
| **Filesystem snapshot not portable (ZFS/btrfs vs ext4/xfs)** | Medium | High | Implement multiple backends: CoW filesystem, overlayfs, user-space copy-on-write; auto-detect at runtime |

---

## Day 1–2 Spike Needed?

**YES** — **Spike: State Backend Matrix** (Owner: State Tech Lead, Due: Day 2 EOD)
- Test matrix: Filesystem (ZFS, btrfs, overlayfs, user-space COW), Database (pg_dump, basebackup, logical replication, CRIU), Network (CRIU, TCP repair mode, user-space proxy)
- Criteria: restore latency, portability, root requirements, container support
- Deliverable: Backend selection matrix + PoC code → `docs/state-backend-decision.md`