# Interview Guide — Project Rewind

**Target:** 20 agent builders / coding-agent users  
**Mix:** 8 startup agent teams, 6 enterprise platform/QA, 4 coding-agent power users, 2 security/compliance  
**Duration:** 30 minutes each  
**Schedule:** Days 2–3, 10/day via Calendly

---

## Introduction Script (2 min)

> "Thanks for taking the time. I'm building a tool called **Rewind** — it lets you capture an agent's full execution (including filesystem, database, network state), replay it deterministically, fork at any point with edited inputs, and export failures as CI tests. This isn't a sales call — I'm trying to understand your actual debugging workflow so we build the right thing. Everything you share stays confidential."

---

## Core Questions (20 min)

### 1. Walk me through your last painful agent failure — how did you debug it? (5 min)

**Probe for:**
- What framework? (LangGraph, Autogen, custom, etc.)
- What failed? (Tool error, hallucination, loop, timeout, wrong output)
- How long to reproduce? (Minutes, hours, "couldn't")
- What tools did you use? (Logs, debugger, print statements, re-run manually)
- What would have helped most?

**Signal:** If they say "I just re-ran it until it worked" → simple replay may be enough. If "I had to restore a DB snapshot manually" → state restore is high value.

---

### 2. What would you pay (time/money) to reproduce that failure reliably? (3 min)

**Probe for:**
- Engineer-hours spent debugging last major failure
- Would they pay $X/month per seat? $X per CI run?
- Would they invest setup time (instrumentation, config) for reliable replay?

**Signal:** Quantifies value. If "I'd spend a day setting it up" → high friction tolerance. If "needs to work in 5 min" → zero-config critical.

---

### 3. Show mock of state-aware fork (3 min)

> **Scenario:** "Your agent breaks a DB at step 40 of 100. Rewind restores step 38 state (DB + files + network). You edit one tool result at step 38. Fork runs from 39 with real tools against restored DB. It works."

**Ask:**
- "Does this match a real scenario you've faced?"
- "What would you edit at the fork point? (Tool output? LLM response? Env var?)"
- "Would you trust the fork result, or still verify manually?"
- "What's the longest fork chain you'd imagine? (Fork of fork of fork?)"

**Signal:** Validates Differentiator #1. If they say "I'd never edit tool output, I'd fix the prompt" → fork editing less critical.

---

### 4. How do you test agents today? Would a failure library change your release process? (5 min)

**Probe for:**
- Current test strategy: Unit? Integration? E2E? Manual? None?
- CI/CD: Do agents run in CI? How flaky?
- "Failure library" = collection of captured failures that become regression tests
- Would they gate merges on "no new failures in library"?

**Signal:** Validates Differentiator #4. If "we don't test agents in CI" → CI integration is aspirational, not immediate.

---

### 5. What would make you switch from your current debugging approach? (4 min)

**Probe for:**
- Must-haves vs nice-to-haves
- Dealbreakers: "Needs to work with [framework]", "Can't require code changes", "Must run in our VPC"
- Integration points: VS Code, Cursor, CLI, GitHub Actions, Datadog
- Team adoption: Solo dev? Team? Org-wide?

**Signal:** Defines MVP scope and integration priorities.

---

## Closing (5 min)

- "Anything I should have asked?"
- "Can I follow up in 2 weeks with a prototype?"
- "Know anyone else I should talk to?" (Referral)

**Capture:** Name, role, company, framework, contact, willingness to pilot (Y/N), key quotes

---

## Interview Scorecard (Fill Immediately After)

| Dimension | Score (1-5) | Evidence |
|-----------|-------------|----------|
| Pain severity (how bad is debugging today?) | | |
| State restore value (would they use fork+edit?) | | |
| CI integration readiness (do they test in CI?) | | |
| Framework lock-in (how hard to adopt new tool?) | | |
| Willingness to pilot (Y/N + enthusiasm) | | |
| **Overall fit** | | |

---

## Scheduling Tracker

| # | Name | Role | Company | Framework | Calendar Link | Status | Scorecard |
|---|------|------|---------|-----------|---------------|--------|-----------|
| 1 | | | | | | ☐ Booked | |
| 2 | | | | | | ☐ Booked | |
| ... | | | | | | | |
| 20 | | | | | | ☐ Booked | |

**Target:** 20 booked by Day 1 EOD. If <10 → Project Lead DMs personally.