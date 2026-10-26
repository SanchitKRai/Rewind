# Contract: State → UI/CLI

**Producer:** State Squad  
**Consumer:** UI & CLI Squad  
**Status:** APPROVED (pending signatures)

---

## State Diff Format

```python
@dataclass
class StateDiff:
    snapshot_a: str           # SnapshotRef ID
    snapshot_b: str           # SnapshotRef ID
    dimensions: List[DimensionDiff]
    summary: DiffSummary

@dataclass
class DimensionDiff:
    dimension: StateDimension
    changes: List[Change]
    metadata: dict            # Dimension-specific info

@dataclass
class Change:
    type: "added" | "removed" | "modified" | "renamed"
    path: str                 # Filesystem path, DB table, socket address, etc.
    before: str | None        # Content hash or serialized value
    after: str | None
    details: dict             # e.g., {"lines_added": 5, "lines_removed": 2}
```

### Dimension-Specific Diff Details

| Dimension | Path Format | Details |
|-----------|-------------|---------|
| FILESYSTEM | `/absolute/path` | `{"size_delta": 1024, "mode_changed": false, "is_binary": true}` |
| DATABASE | `postgresql://db/table/row_id` | `{"columns_changed": ["status", "updated_at"], "row_count_delta": 0}` |
| NETWORK | `tcp://127.0.0.1:5432` | `{"state_before": "ESTABLISHED", "state_after": "TIME_WAIT", "bytes_sent_delta": 2048}` |
| PROCESS | `pid:1234` | `{"fd_changes": [{"fd": 3, "path": "/tmp/x", "action": "closed"}], "mem_delta_mb": 5}` |
| KERNEL | `sysctl:net.ipv4.tcp_tw_reuse` | `{"value_before": "0", "value_after": "1"}` |
| TIME | `clock:monotonic` | `{"offset_ns": 123456789}` |

---

## Sandbox Status Events (Streaming)

```python
class SandboxStatus(Enum):
    PREPARING = "preparing"       # Setting up restore
    RESTORING = "restoring"       # Actively restoring dimensions
    READY = "ready"               # Restore complete, awaiting replay
    REPLAYING = "replaying"       # Replay engine executing steps
    PAUSED = "paused"             # At breakpoint / divergence / user pause
    DIVERGED = "diverged"         # Output differs from capture
    COMPLETED = "completed"       # Replay finished
    FAILED = "failed"             # Restore or replay error

@dataclass
class SandboxEvent:
    event_type: "status" | "progress" | "diff" | "divergence" | "log"
    sandbox_id: str
    timestamp_ns: int
    status: SandboxStatus | None
    progress: ProgressInfo | None
    diff: StateDiff | None
    divergence: DivergenceInfo | None
    log: str | None

@dataclass
class ProgressInfo:
    phase: "snapshot" | "restore" | "replay"
    current_step: int
    total_steps: int
    current_dimension: StateDimension | None
    dimension_progress: float  # 0.0-1.0

@dataclass
class DivergenceInfo:
    step_seq: int
    expected: EventSummary
    actual: EventSummary
    policy: SideEffectPolicy
```

---

## Progress Streaming API

```python
class StateProgressStream:
    """WebSocket / Server-Sent Events endpoint for UI."""
    
    async def subscribe(self, sandbox_id: str) -> AsyncIterator[SandboxEvent]:
        """Stream events for a sandbox session."""
    
    async def get_current_state(self, sandbox_id: str) -> SandboxState:
        """Polling fallback: current status + latest diff."""
```

**Event ordering guarantee:** Events delivered in timestamp order. `status` events are coarse-grained; `progress` events are fine-grained (every dimension, every 100ms during restore).

---

## CLI Integration Points

```bash
# State inspection commands (implemented by UI/CLI squad, calling State API)
rewind state list --run <run_id>                    # List snapshots
rewind state diff <snap_a> <snap_b> --dimension fs  # Show diff
rewind state inspect <snapshot_id> --dimension db   # Pretty-print DB state
rewind state export <snapshot_id> --format tar|sql  # Export for CI
```

---

## Signatures

| Role | Name | Signature | Date |
|------|------|-----------|------|
| State Tech Lead | | | |
| UI Tech Lead | | | |
| Project Lead | | | |