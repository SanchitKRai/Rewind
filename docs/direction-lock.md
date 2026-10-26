# Direction Lock v1 — Project Rewind

**Date:** 2026-10-26  
**Owner:** Project Lead  
**Status:** SIGNED OFF

---

## One-Sentence Differentiator

> **We are the only tool that restores environment state for forks, captures at the system boundary, handles concurrency, and turns failures into CI tests — all together.**

---

## Four Non-Negotiable Differentiators (Scope for 5 Days)

| # | Differentiator | Description | Success Signal (Day 5) |
|---|----------------|-------------|------------------------|
| 1 | **State-Aware Forking** | Fork an agent execution at any step with full environment state (files, DB, network sockets, process tree) restored | Can fork at step 40 of 100, edit tool result at step 38, re-run from 39 with real tools against restored DB |
| 2 | **Boundary-Level Capture** | Capture at the system boundary (syscalls, network, filesystem) — not instrumentation — so it works on any agent framework without code changes | Captures `strace`-level events from a black-box binary agent; no SDK required |
| 3 | **Concurrency Support** | Correctly captures and replays concurrent/parallel agent execution (multiple threads, async tasks, subprocesses) with happens-before ordering | Replays a LangGraph parallel branch execution with correct causal ordering |
| 4 | **Failure-to-CI Workflow** | One-click export of any failure replay as a deterministic CI test (GitHub Actions, GitLab CI, pytest) | Exported test reproduces the failure in CI without the original environment |

---

## Pivot Triggers (from Section 13.6)

| Trigger | Condition | Pivot Action |
|---------|-----------|--------------|
| **Competitor ships state-aware cross-framework forking** | A competitor releases cross-framework state-aware forking with boundary capture before our Day 5 demo | Pivot to *specialized vertical* (e.g., "Rewind for LangGraph only") or *infrastructure layer* (sell capture engine to other tools) |
| **Interviews show simple replay is "good enough"** | >60% of 20 interviewees say they'd use simple replay (no state restore) and won't pay for state | Pivot to *lightweight replay + sharing* (drop state restore, focus on collaboration) |
| **State capture too slow/fragile** | Capture overhead >15% latency or >5% crash rate on benchmark agents (Day 3 spike) | Pivot to *selective capture* (opt-in critical sections only) or *post-hoc reconstruction* from logs |
| **Early testers don't return** | <30% of Day 3-4 pilot users run a second session within 48 hours | Pivot to *CI-only mode* (drop interactive UI, focus on automated regression detection) |

---

## Signatures

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Project Lead | | ✅ | 2026-10-26 |
| Tech Lead — Capture | | | |
| Tech Lead — Replay Engine | | | |
| Tech Lead — State | | | |
| Tech Lead — UI & CLI | | | |
| Tech Lead — DevEx & Launch | | | |
| DevEx Lead | | | |

---

## Notes

- This document is the **single source of truth** for scope. Any feature not traceable to one of the 4 differentiators is out of scope for the 5-day sprint.
- Pivot triggers are evaluated at **Day 3 mid-sprint review** and **Day 5 retrospective**.
- If a pivot triggers, we rewrite this document and re-sign within 2 hours.