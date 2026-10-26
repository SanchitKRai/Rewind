# Day 3 Battle Plan — Project Rewind

**Date:** 2026-10-28  
**Status:** ☐ Draft after Day 2 gate  
**Source:** [`decision-day2-go-nogo.md`](decision-day2-go-nogo.md)

Day 3 priorities must follow the Day 2 decision and the debt list. Do not restore deferred Day 1 scope without a new decision.

## Decision-dependent priorities

| Priority | Work | Owner | Acceptance test | Status |
|----------|------|-------|-----------------|--------|
| P0 | Stabilize the capture-to-log path | | Record and reload the headline run | ☐ |
| P0 | Make strict replay hash-stable | | `rewind verify` passes twice | ☐ |
| P0 | Make filesystem restore reliable | | Fork restores the exact repository state | ☐ |
| P1 | Improve fork editing UX | | `$EDITOR` edit produces a new run | ☐ |
| P1 | Convert strongest interview evidence into a product test | | One user scenario reproduced end-to-end | ☐ |

## Day 3 scope changes

- GO scope retained:
- Scope narrowed:
- Pivot work, if any:

## Retro actions

| Lead | What was fragile | What was solid | Top two fixes |
|------|------------------|-----------------|---------------|
| Capture | | | |
| Replay | | | |
| State | | | |
| UI/CLI | | | |
| DevEx | | | |

## Signatures

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Project Lead | | | |
| Capture Lead | | | |
| Replay Lead | | | |
| State Lead | | | |
| UI/CLI Lead | | | |
| DevEx Lead | | | |