# Bug Fixer

An AI agent skill that enforces disciplined bug fixing — diagnosis before action, right-sized scope, minimal regression risk, and documentation that stays current. It prevents the three most common fix failures: hacks that create future bugs, scope creep that creates current bugs, and untested changes that create mystery bugs.

---

## The Problem

- Most bug fixes target **symptoms, not root causes** — the same bug resurfaces weeks later in a different form
- Developers under pressure ship **hotfixes that become permanent** — tech debt compounds silently
- "While I'm here" refactors during bug fixes introduce **unrelated regressions** that are harder to diagnose than the original bug
- Fixes land without tests, without documentation updates, and without blast radius assessment — **the next developer inherits a minefield**

Fixing bugs well is a discipline. This skill encodes that discipline so AI agents follow it by default.

---

## What This Skill Does

Bug Fixer acts as a structured methodology layer that guides AI agents through the full fix lifecycle. When activated, it enforces a 5-phase process that prevents the agent from jumping to code changes before understanding the problem.

### Core Capabilities

| Capability | What It Does |
|-----------|-------------|
| **Pre-Flight Grounding** | Hard gate: agent must reproduce, read code, map blast radius, and check for related issues before writing any fix |
| **Root Cause Analysis** | Guides asking "why" until the true cause is found — not just "what" is broken |
| **Scope Assessment** | Decision framework to choose between narrow fix, wider improvement, or hotfix — prevents both hacks and scope creep |
| **Blast Radius Mapping** | Identifies direct impact, indirect dependencies, existing test coverage, and new tests needed |
| **Hotfix Protocol** | Strict requirements for temporary fixes: follow-up ticket, code comments, replacement timeline |
| **Anti-Pattern Detection** | Flags symptom fixing, shotgun debugging, gold plating, permanent hotfixes, and silent fixes |
| **Documentation Triggers** | Prompts Context Brain updates when behavior changes, patterns were wrong, or missing docs caused the bug |

### Three Fix Strategies

```
Can narrow fix address root cause without being a hack?
├── YES → Narrow Fix (default goal)
└── NO → Is there time to do it right?
         ├── YES → Wider Improvement (with justification)
         └── NO → Hotfix + Follow-up (with protocol)
```

---

## Skill Architecture

```
bugfixer-skill/
└── SKILL.md           # Complete skill (~460 lines)
    ├── Pre-flight grounding & check-in
    ├── Core philosophy
    ├── 5-phase bug fix process
    │   ├── Phase 1: Diagnose (reproduce, root cause, blast radius)
    │   ├── Phase 2: Assess Scope (hack vs. goldilocks vs. scope creep)
    │   ├── Phase 3: Select Strategy (narrow / wider / hotfix)
    │   ├── Phase 4: Execute (minimal change, test, verify)
    │   └── Phase 5: Documentation Check (Context Brain integration)
    ├── Hotfix protocol & anti-abuse detection
    ├── Anti-patterns table
    └── Quick reference
```

Single-file skill — no reference files needed. The full methodology fits in one document because bug fixing is a process skill, not a domain knowledge skill.

---

## The 5-Phase Process

```
┌─────────────────────┐
│ 1. DIAGNOSE         │ → Understand before acting
└─────────┬───────────┘
          ▼
┌─────────────────────┐
│ 2. ASSESS SCOPE     │ → Determine fix boundaries
└─────────┬───────────┘
          ▼
┌─────────────────────┐
│ 3. SELECT STRATEGY  │ → Choose approach
└─────────┬───────────┘
          ▼
┌─────────────────────┐
│ 4. EXECUTE          │ → Implement with guardrails
└─────────┬───────────┘
          ▼
┌─────────────────────┐
│ 5. DOCUMENTATION    │ → Context Brain check
└─────────────────────┘
```

Phases are sequential. The skill enforces order — no skipping ahead to implementation.

---

## Pre-Flight Grounding

Before any bug-related work — including planning, diagnosing, or answering questions — the agent must complete a check-in and produce a **Grounded Statement**:

```
Bug: [brief description of the defect]
Root Cause: [identified or suspected root cause]
Blast Radius: [files/components affected] — [key dependencies]
Related Context: [past fixes, related tickets, known debt] — [relevant patterns]
```

This is a hard gate. It applies even in plan mode.

---

## Example Usage

### Bug Ticket

**Prompt:**
> Users report that the search filter resets when they navigate back to the results page. Fix this bug.

**The skill will:**
1. Diagnose — Reproduce the state loss, trace the filter state lifecycle, identify whether it's a state management issue (unmount clearing state) or a navigation issue (missing query params)
2. Assess — Determine if a narrow fix (persist to URL params) is sufficient or if the state management pattern is broken more broadly
3. Select — Recommend narrow fix or wider improvement based on assessment
4. Execute — Implement minimal change, add test for the specific scenario
5. Document — Flag if component behavior docs need updating

### Production Incident

**Prompt:**
> Production is down — the payment endpoint is returning 500s after the last deploy. Hotfix needed.

**The skill will:**
1. Skip to hotfix protocol given critical urgency
2. Identify the breaking change from the last deploy
3. Implement minimal stabilizing fix
4. Require: follow-up ticket created, `HOTFIX:` comment in code, timeline for proper fix
5. Flag if the hotfix pattern is being overused in this area

---

## Anti-Patterns

The skill detects and prevents these common bug-fixing failures:

| Anti-Pattern | What It Looks Like | Why It's Wrong |
|-------------|-------------------|----------------|
| **Symptom Fixing** | Fix masks the problem without addressing cause | Bug resurfaces elsewhere |
| **Shotgun Debugging** | Change random things until it works | Creates new bugs, no understanding |
| **Gold Plating** | "While I'm here" refactors beyond scope | Scope creep, unnecessary risk |
| **Untested Fixes** | "It works on my machine" | Regression waiting to happen |
| **Permanent Hotfixes** | Temporary fix becomes permanent | Tech debt accumulation |
| **Silent Fixes** | No documentation of what changed or why | Future maintainers left confused |

---

## Limitations

- **Cannot run code.** The skill guides the agent through diagnosis and fix methodology, but cannot execute tests or reproduce bugs itself — the agent's tooling handles that.
- **Cannot replace domain knowledge.** The skill provides process discipline, not domain expertise. It won't know that your payment service has a 30-second timeout unless the agent reads the code.
- **Hotfix protocol requires ticket system access.** The skill prompts for follow-up tickets but cannot create them without appropriate tooling.
- **Documentation triggers assume Context Brain.** Phase 5 references AGENTS.md, Domain Guides, and ADRs. If the project doesn't use Context Brain, the agent should adapt the documentation check to whatever system is in place.

---

## Integration

This skill integrates with **Context Brain** (via the Brain Keeper skill) for documentation updates. When a fix changes component behavior or reveals incorrect patterns, Bug Fixer prompts the appropriate documentation updates:

| Change | Update |
|--------|--------|
| Behavior changed | Component AGENTS.md |
| Pattern was wrong | Domain Guide |
| New architectural insight | Consider ADR |
| Missing context caused bug | Add it now |

---

## Installation

Add `SKILL.md` to your AI agent's skills directory:

```
bugfixer-skill/
└── SKILL.md
```

The skill activates automatically when the agent encounters bug-related work — bug tickets, error reports, broken behavior, failing tests, or production incidents.

---

Built by Space Dinosaurs Engineering. Because fixing bugs is a discipline, not a dice roll.
