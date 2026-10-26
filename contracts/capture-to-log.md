# Contract: Capture → Log

**Producer:** Capture Squad  
**Consumer:** Replay Engine Squad  
**Status:** APPROVED (pending signatures)

---

## Event Schema (JSON)

```json
{
  "event_id": "uuid-v7",
  "timestamp_ns": 1234567890123456789,
  "sequence": 42,
  "thread_id": "thread-7",
  "event_type": "syscall|network|filesystem|time|random|signal|snapshot",
  "syscall": {
    "nr": 257,
    "name": "openat",
    "args": {"dirfd": -100, "pathname": "/home/user/.config", "flags": 0, "mode": 0},
    "return": 3,
    "errno": 0
  },
  "network": {
    "direction": "send|recv",
    "protocol": "tcp|udp|unix",
    "local_addr": "127.0.0.1:5432",
    "remote_addr": "127.0.0.1:49152",
    "payload_sha256": "a3f2...",
    "payload_length": 1024
  },
  "filesystem": {
    "operation": "read|write|open|close|stat|unlink|rename",
    "path": "/home/user/data.json",
    "offset": 0,
    "length": 512,
    "content_sha256": "b4e1..."
  },
  "time": {
    "call": "clock_gettime|gettimeofday|time",
    "value_ns": 1234567890123456789
  },
  "random": {
    "call": "getrandom|urandom|rand",
    "bytes_sha256": "c5d2...",
    "length": 32
  },
  "signal": {
    "signo": 15,
    "code": "SI_USER",
    "sender_pid": 1234
  },
  "snapshot": {
    "trigger": "periodic|checkpoint|manual|fork-point",
    "state_ref": "sha256:abc123...",
    "includes": ["fs", "db", "net", "proc", "kernel"]
  },
  "happens_before": ["event-id-1", "event-id-2"],
  "vector_clock": {"thread-1": 10, "thread-7": 42, "thread-3": 5}
}
```

**Required fields:** `event_id`, `timestamp_ns`, `sequence`, `thread_id`, `event_type`  
**One of:** `syscall`, `network`, `filesystem`, `time`, `random`, `signal`, `snapshot` must be present

---

## Content-Addressing Scheme

- **Event store:** Append-only log, one JSON line per event
- **Chunking:** 64KB chunks, content-addressed by SHA256(chunk_bytes)
- **Index:** `index.jsonl` — one line per chunk: `{"chunk_hash": "...", "first_seq": 100, "last_seq": 147, "byte_offset": 0}`
- **Blob store:** Large payloads (>1KB) stored separately at `blobs/sha256/<hash>`; event references via `payload_sha256`

---

## Happens-Before Edges

- **Vector clocks** per thread (hybrid logical clocks for distributed agents)
- **Explicit edges** in `happens_before` array for cross-thread synchronization (mutex, channel, join)
- **Capture layer responsibility:** Emit edges for every observed synchronization primitive
- **Replay layer responsibility:** Enforce ordering; block replay step until all `happens_before` predecessors are replayed

---

## Streaming Chunk Format

```
[Chunk Header: 32 bytes]
  magic: "RWND" (4 bytes)
  version: 1 (1 byte)
  flags: 0 (1 byte)  // bit 0 = compressed (zstd)
  chunk_seq: 123 (8 bytes, LE)
  event_count: 47 (4 bytes, LE)
  uncompressed_size: 65536 (8 bytes, LE)
  compressed_size: 18432 (8 bytes, LE)
  chunk_sha256: 32 bytes
[Chunk Payload: compressed_size bytes]
  zstd-compressed JSONL events
```

---

## API (Consumer View)

```python
# Replay Engine consumes via:
class CaptureLogReader:
    def __init__(self, log_dir: Path): ...
    
    def iter_events(self, from_seq: int = 0) -> Iterator[Event]: ...
    
    def get_event(self, seq: int) -> Event: ...
    
    def get_chunk(self, chunk_hash: str) -> bytes: ...
    
    def get_index(self) -> List[ChunkIndexEntry]: ...
```

---

## Signatures

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Capture Tech Lead | | | |
| Replay Tech Lead | | | |
| Project Lead | | | |