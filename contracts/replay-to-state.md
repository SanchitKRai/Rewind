# Contract: Replay → State

**Producer:** Replay Engine Squad  
**Consumer:** State Squad  
**Status:** APPROVED (pending signatures)

---

## Snapshot Trigger Points

```python
class SnapshotTrigger(Enum):
    PERIODIC = "periodic"           # Every N steps (configurable)
    CHECKPOINT = "checkpoint"       # At agent-defined checkpoints
    FORK_POINT = "fork_point"       # Before any fork operation
    MANUAL = "manual"               # CLI/API request
    PRE_TOOL_CALL = "pre_tool_call" # Before each tool invocation
    POST_TOOL_CALL = "post_tool_call" # After each tool invocation
```

**Default policy:** `PERIODIC(every 10 steps)` + `FORK_POINT` + `PRE_TOOL_CALL` for tools marked `side_effect=true`

---

## Restore API

```python
class StateRestore:
    """Called by Replay Engine to restore environment before replaying from a fork point."""
    
    async def restore(self, request: RestoreRequest) -> RestoreResult:
        """
        Restores the full environment state to match the snapshot.
        
        Blocks until all dimensions are restored and consistent.
        """
    
    async def diff(self, snapshot_a: str, snapshot_b: str) -> StateDiff:
        """Compute diff between two snapshots for UI display."""
    
    async def list_snapshots(self, run_id: str) -> List[SnapshotRef]:
        """All available snapshots for a run."""

@dataclass
class RestoreRequest:
    run_id: str
    snapshot_ref: str           # Content-addressed snapshot ID
    target_pid: int | None      # If restoring into existing process tree
    side_effect_policy: SideEffectPolicy
    fork_id: str | None         # If this restore is for a fork

@dataclass
class RestoreResult:
    success: bool
    restored_dimensions: List[StateDimension]
    warnings: List[str]         # e.g., "TCP sockets restored in TIME_WAIT"
    restore_time_ms: int

@dataclass
class SnapshotRef:
    snapshot_id: str            # SHA256 of snapshot manifest
    trigger: SnapshotTrigger
    step_seq: int
    timestamp_ns: int
    dimensions: List[StateDimension]
    size_bytes: int
```

---

## State Dimensions

```python
class StateDimension(Enum):
    FILESYSTEM = "filesystem"       # Working directory + relevant paths
    DATABASE = "database"           # PostgreSQL, Redis, SQLite, etc.
    NETWORK = "network"             # Open sockets, connection state, listen ports
    PROCESS = "process"             # Process tree, file descriptors, memory maps
    KERNEL = "kernel"               # Sysctl, namespace, cgroup, time
    TIME = "time"                   # Wall clock, monotonic, RTC
```

**Each dimension must implement:**
- `snapshot() -> SnapshotData`
- `restore(SnapshotData) -> RestoreResult`
- `diff(SnapshotData, SnapshotData) -> DimensionDiff`

---

## Side-Effect Policy Enum

```python
class SideEffectPolicy(Enum):
    ALLOW = "allow"           # Execute normally (network calls go out, files written)
    DENY = "deny"             # Block and return error (ECONNREFUSED, EACCES)
    RECORD = "record"         # Execute but capture new events for divergence detection
    MOCK = "mock"             # Return recorded response from original capture
```

**Default for forks:** `RECORD` — allows divergence detection while protecting production systems

**CLI override:** `--side-effect-policy=allow|deny|record|mock`

---

## Signatures

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Replay Tech Lead | | | |
| State Tech Lead | | | |
| Project Lead | | | |