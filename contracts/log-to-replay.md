# Contract: Log → Replay

**Producer:** Replay Engine Squad  
**Consumer:** Replay Engine Squad (self) / State Squad / UI & CLI Squad  
**Status:** APPROVED (pending signatures)

---

## Query API

```python
class ReplayLogQuery:
    """Read-only query interface over captured log."""
    
    def get_run(self, run_id: str) -> RunMetadata:
        """Returns: run_id, start_time, end_time, total_steps, agent_config, capture_config"""
    
    def get_step(self, run_id: str, seq: int) -> StepView:
        """Returns: event, step_index, causal_ancestors[], divergent_forks[]"""
    
    def get_causal_ancestors(self, run_id: str, seq: int, max_depth: int = 10) -> List[StepView]:
        """Walks happens-before edges backwards; returns ordered list"""
    
    def get_divergent_paths(self, run_id: str, fork_point_seq: int) -> List[ForkPath]:
        """Returns all forks created from this fork point with their outcomes"""
    
    def search_events(self, run_id: str, filter: EventFilter) -> List[StepView]:
        """Filter by: event_type, thread_id, syscall_name, path_glob, time_range"""
    
    def stream_events(self, run_id: str, from_seq: int) -> AsyncIterator[Event]:
        """For live tail / progressive loading"""
```

---

## Data Structures

```python
@dataclass
class RunMetadata:
    run_id: str
    agent_type: str           # "langgraph", "autogen", "custom", "binary"
    agent_config: dict        # Original agent invocation config
    capture_config: dict      # What was captured (syscalls, network, etc.)
    start_time_ns: int
    end_time_ns: int
    total_events: int
    total_steps: int
    status: "completed" | "failed" | "running"

@dataclass
class StepView:
    seq: int
    event: Event              # From capture-to-log schema
    step_index: int           # Logical step number (1-based, increments on LLM/tool boundary)
    thread_id: str
    causal_ancestors: List[int]  # Seq numbers
    divergent_forks: List[str]   # Fork IDs that diverged from this step

@dataclass
class ForkPath:
    fork_id: str
    fork_point_seq: int
    edited_inputs: dict       # What was changed at fork point
    outcome: "success" | "failure" | "running"
    divergence_seq: int | None  # Where it first diverged from parent
```

---

## Divergence Detection Hook

```python
# Called by Replay Engine when replay execution produces different output than captured
class DivergenceDetector:
    def on_divergence(
        self,
        run_id: str,
        expected_event: Event,
        actual_event: Event,
        fork_context: ForkContext | None
    ) -> DivergenceAction:
        """
        expected_event: What the original capture recorded
        actual_event: What the replay just produced
        fork_context: If this replay is a fork, info about the fork
        
        Returns: DivergenceAction.CONTINUE | DivergenceAction.PAUSE | DivergenceAction.ABORT
        """
```

**Hook registration:** Replay Engine calls this after every step where output differs from capture.

---

## Signatures

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Replay Tech Lead | | | |
| State Tech Lead | | | |
| UI Tech Lead | | | |
| Project Lead | | | |