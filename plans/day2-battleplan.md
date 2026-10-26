# Day 2 Battle Plan — Project Rewind

**Date:** 2026-10-27  
**Owner:** Project Lead  
**Status:** COMMITTED — POC scope locked before implementation

## Day 2 outcome

Build the smallest end-to-end proof of state-aware replay, then validate whether the problem is valuable enough to continue. No new feature enters the POC after the architecture decision at 09:30.

## Schedule

| Time | Activity | Participants | Output |
|------|----------|--------------|--------|
| 09:00–09:30 | POC architecture decision | Project Lead + 5 Tech Leads | [`poc-scope.md`](poc-scope.md), signed |
| 09:30–17:00 | Parallel squad sprints | Capture, Replay, State, UI/CLI, DevEx | Working slices, interview notes, evidence |
| 16:00–18:00 | Integration pair | One dev per squad + Project Lead | Headline demo and new fork run |
| 18:00–18:45 | Go/no-go gate | Project Lead + 5 Tech Leads + DevEx Lead | [`decision-day2-go-nogo.md`](decision-day2-go-nogo.md) |
| 18:45–19:15 | Retro and Day 3 planning | Project Lead + 5 Tech Leads | [`day3-battleplan.md`](day3-battleplan.md) |

## Squad sprint contracts

| Squad | People | Day 2 goal | Required evidence |
|-------|--------|------------|-------------------|
| Capture | 5 devs | Proxy records one Claude Code run faithfully | Replayable JSONL log, redaction check, 20-step E2E run |
| Replay Engine | 5 devs | Strict replay and hash verification | `rewind verify <run>` passes; divergence stops at first mismatch |
| State | 4 devs | Linux filesystem snapshot and restore | 10k-file benchmark: snapshot <500ms, restore <2s, overhead <10% |
| UI/CLI | 3 devs | Headline CLI workflow | `record`, `replay`, `fork`, and `timeline` work |
| DevEx | 3 devs | Validate demand and assumptions | 10 interviews, 6 scored assumptions, tally of unprompted yes/maybe/no |

## Integration sequence

1. Record a 15-step Claude Code run that edits a file and fails a test.
2. Replay the run with recorded LLM and tool responses only; verify hashes.
3. Fork at step 10, edit the tool result, restore the repository, and resume with live tools.
4. Confirm the fork passes and writes a new run log.
5. Run the exact headline demo in [`poc-scope.md`](poc-scope.md).

## Completion gate

The day is complete only when the headline demo and assumption scorecard are populated. A partial demo or missing evidence defaults to **PIVOT**, unless the decision matrix in [`decision-day2-go-nogo.md`](decision-day2-go-nogo.md) explicitly supports **GO**.

| Deliverable | Location | Owner | Status |
|-------------|----------|-------|--------|
| POC scope lock | [`poc-scope.md`](poc-scope.md) | Project Lead + Leads | ☐ |
| Capture log and demo | Capture evidence | Capture Lead | ☐ |
| Strict replay and verify | Replay evidence | Replay Lead | ☐ |
| State benchmark | State evidence | State Lead | ☐ |
| CLI demo | CLI evidence | UI/CLI Lead | ☐ |
| Headline demo | Integration evidence | All Leads | ☐ |
| Ten interview notes | [`../research/interviews-day2/`](../research/interviews-day2/) | DevEx | ☐ |
| Assumption scores | [`../research/assumption-scores.md`](../research/assumption-scores.md) | DevEx Lead | ☐ |
| Go/no-go decision | [`decision-day2-go-nogo.md`](decision-day2-go-nogo.md) | Project Lead | ☐ |
| Technical debt | [`tech-debt-day2.md`](tech-debt-day2.md) | Project Lead + Leads | ☐ |
| Day 3 plan | [`day3-battleplan.md`](day3-battleplan.md) | Project Lead + Leads | ☐ |

## Scope discipline

The prior architecture spikes, web debugger, cross-platform capture, multi-agent integrations, and package distribution work are deferred. They may not be reintroduced during Day 2 integration.