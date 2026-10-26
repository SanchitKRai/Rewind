# POC Scope Lock — Project Rewind

**Date:** 2026-10-27  
**Status:** ☐ Proposed  ☐ Locked  
**Decision meeting:** 09:00–09:30

This document is the Day 2 scope boundary. Anything in the “Out” column is deferred to Day 3 or later and requires a new decision.

| Component | In POC | Out of POC |
|-----------|--------|------------|
| Capture | Proxy for one Claude Code agent; LLM and tool calls; content-addressed log | MCP proxy, HTTP proxy, kernel capture, SDK shims |
| Log | Event JSONL with hash, parent hash, causal edges, timestamp | Event graph implementation, JSON file storage, streaming chunks, Git-like object store, deduplication, versioned format |
| Replay | Strict replay serving recorded LLM and tool responses; divergence detection by hash | Regression replay, counterfactual execution, fork with live model |
| State | Copy-on-write filesystem snapshot and restore at step boundaries | Database branching, network isolation, port remapping, microVM |
| UI/CLI | `record`, `replay`, `fork <run> <step> --edit-tool-result`, timeline printer | Web UI, side-by-side diff, cost/token overlay, export |
| Integrations | One Claude Code agent, one repository, SQLite | Cursor, Copilot, LangGraph, PostgreSQL, multi-agent |

## Acceptance criteria

- `rewind record -- claude-code "fix the login bug"` starts capture and writes a run log.
- Every tool call records name, input, output, duration, and exit code.
- Secrets, API keys, and configured PII are redacted before persistence.
- `rewind replay <run>` makes no live LLM or tool calls and verifies every recorded step.
- `rewind verify <run>` reports a clear PASS or the first mismatching step.
- `rewind fork <run> <step> --edit-tool-result` opens `$EDITOR`, restores state, and runs the fork with live tools.
- `rewind timeline <run>` prints numbered steps, tool names, durations, and status.
- The fork produces a new run log after the edited result fixes the failing test.

## Headline demo

```bash
rewind record -- claude-code "fix the login bug"
rewind timeline run-abc123
rewind fork run-abc123 38 --edit-tool-result
rewind test create run-abc123 --name "login-bug-regression"
```

## Signatures

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Project Lead | | | |
| Capture Lead | | | |
| Replay Lead | | | |
| State Lead | | | |
| UI/CLI Lead | | | |
| DevEx Lead | | | |