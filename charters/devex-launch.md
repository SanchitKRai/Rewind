# Squad Charter: DevEx & Launch

**Tech Lead:** [Name]  
**Mission:** Own developer experience from install to first value — packaging, distribution, documentation, onboarding, and the Day 5 public launch (blog, demo video, Hacker News, Discord).

---

## OKRs (Day 5 Outcomes)

| OKR | Metric | Target |
|-----|--------|--------|
| **OKR 1: Zero-Friction Install** | `brew install rewind` / `pipx install rewind` / `npm i -g rewind` all work on clean machine | 3/3 package managers publish successfully |
| **OKR 2: Time-to-First-Replay** | Fresh clone → capture → replay → fork → edit → verify in <10 min | Measured with 3 external users Day 5 |
| **OKR 3: Launch Reach** | Blog post + demo video + HN post + Discord community | >500 unique visitors, >50 Discord joins, >20 GitHub stars |

---

## Dependencies on Other Squads

| Need From | Squad | Required By | Details |
|-----------|-------|-------------|---------|
| CLI binary artifacts (Linux, macOS, Windows) | UI & CLI | Day 4 EOD | `rewind` binary + shell completions |
| Capture/Replay config schema for docs | Capture / Replay | Day 3 EOD | All flags, env vars, config file format |
| State backend requirements (root, kernel version) | State | Day 3 EOD | System requirements for install docs |
| Interview insights for positioning/messaging | All (via DevEx) | Day 3 EOD | Top 3 pain points, language users use |

---

## Top 3 Risks + Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| **Packaging delays (Homebrew PR review, PyPI 2FA, npm org)** | Medium | High | Pre-create orgs Day 1; submit Homebrew PR Day 3; use `pipx run --spec` for immediate test |
| **Launch assets (video, blog) rushed and low quality** | High | Medium | Start Day 3: record demo script, outline blog; Day 4: polish; Day 5: publish |
| **No external users for Day 5 validation** | Medium | High | Recruit 5 "friendly users" from interviews (Day 2-3) for Day 5 dry-run |

---

## Day 1–2 Spike Needed?

**YES** — **Spike: Distribution Strategy** (Owner: DevEx Tech Lead, Due: Day 2 EOD)
- Decide: single binary (Go/Rust) vs Python package + native extensions vs npm + native
- Compare: `PyOxidizer` vs `Nuitka` vs `Go` + `cgo` vs `Rust` + `pyo3`
- Criteria: binary size, cross-compile ease, plugin/extensibility, team expertise
- Deliverable: Hello-world package in each format → `docs/distribution-decision.md`