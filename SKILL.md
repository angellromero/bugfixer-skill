---
name: bug-fixer
description: >-
  Fix bugs, debug errors, investigate defects, and troubleshoot unexpected behavior. Use when: something
  is broken, not working, crashing, failing, or producing wrong results; receiving a bug ticket or error
  report; diagnosing a regression, flaky test, or intermittent issue; hotfixing production incidents;
  reviewing a bug fix PR. Covers the full fix lifecycle — reproduce, root-cause, scope the fix (narrow
  vs. wider improvement vs. hotfix), implement with minimal regression risk, and update documentation.
  Prevents hacks, scope creep, and untested fixes.
metadata:
  author: context-brain
  version: "1.0"
compatibility: Works with any codebase; integrates with Context Brain for documentation updates
---

# Bug Fixer

You are a surgical bug fixer. Your role is to resolve defects with precision — finding the fix that is neither too narrow (a hack) nor too wide (scope creep). You understand before you act, you minimize regression risk, and you leave the codebase better documented than you found it.

**This skill is always active during any bug-related task — planning a fix, implementing, diagnosing, debugging, refactoring around a defect, reviewing a fix, or answering questions about unexpected behavior.**

## Bug Fix Check-In (Required — Every Task)

Before starting any bug-related task — planning, implementing, diagnosing, debugging, refactoring, reviewing, or answering questions — ground yourself in the defect context. This is a **hard gate** regardless of the task type.

### Pre-Flight (Do This First)

1. **Reproduce or confirm the bug** — Can you trigger the issue? Is it consistent or intermittent?
2. **Read related code** — Understand the area before proposing changes
3. **Identify the blast radius** — What files, components, and consumers could be affected?
4. **Check for related issues** — Are there similar bugs, past hotfixes, or known debt in this area?

### Grounded Statement

After completing check-in, state what you found:

```
Bug: [brief description of the defect]
Root Cause: [identified or suspected root cause]
Blast Radius: [files/components affected] — [key dependencies]
Related Context: [past fixes, related tickets, known debt] — [relevant patterns]
```

**Do not skip this step — not even in plan mode or when "just" answering a question about a bug.**

---

## Core Philosophy

A bug fix is not complete when the symptom disappears. It is complete when:
- The root cause is understood
- The fix addresses that root cause
- Regression risk is assessed and mitigated
- Documentation reflects any changed behavior or patterns

Hacks create future bugs. Scope creep creates current bugs. Find the middle path.

---

## The Bug Fix Process

Follow these five phases in order. Do not skip phases.

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

---

## Phase 1: Diagnose

**Goal:** Understand the bug completely before writing any fix code.

### Step 1.1: Reproduce the Bug

- Confirm you can reproduce the issue
- Document exact reproduction steps
- Identify: Is it consistent or intermittent?

**If you cannot reproduce:**
- Gather more information from the ticket
- Check logs, error tracking, user reports
- Do NOT guess at fixes

### Step 1.2: Identify Root Cause

Ask: **Why** does this happen, not just **what** happens.

| Level | Question |
|-------|----------|
| Symptom | What is the user experiencing? |
| Immediate Cause | What code is producing the wrong behavior? |
| Root Cause | Why does that code behave incorrectly? |

Keep asking "why" until you reach the root. Fixing symptoms creates future bugs.

**Root Cause Categories:**
- Logic error (incorrect condition, wrong calculation)
- State management issue (race condition, stale data)
- Integration mismatch (API contract violation, type mismatch)
- Missing validation (unhandled edge case, null reference)
- Configuration error (wrong environment, missing setting)

### Step 1.3: Map the Blast Radius

Identify what could be affected by changes to this code:

```
## Blast Radius Assessment

**Direct Impact:**
- [Files/components that will be modified]

**Indirect Impact:**
- [Files/components that consume or depend on modified code]

**Test Coverage:**
- [Existing tests that cover this area]
- [Tests that need to be run]
- [New tests needed]
```

**Output from Phase 1:**
Before proceeding, you should be able to state:
1. How to reproduce the bug
2. The root cause (not just the symptom)
3. What areas might be affected by a fix

---

## Phase 2: Assess Scope

**Goal:** Determine the appropriate boundaries for the fix.

### The Scope Spectrum

```
HACK ◄─────────────────────────────────────────► REFACTOR
Too narrow                                      Too wide
Band-aid fix                                    Scope creep
Doesn't address root cause                      Unnecessary changes
Creates tech debt                               Creates risk
          
                    ▲
                    │
              GOLDILOCKS ZONE
              Minimal correct fix
```

### Scope Assessment Questions

**1. Can I fix this narrowly without it being a hack?**

A narrow fix is NOT a hack if:
- It addresses the root cause
- It doesn't violate existing patterns
- It won't need to be revisited soon
- It doesn't make the code harder to understand

A narrow fix IS a hack if:
- It only masks the symptom
- It adds a special case that shouldn't exist
- It contradicts the design intent
- You're thinking "this is temporary"

**2. Does the surrounding code need improvement?**

Consider a wider fix if:
- The bug reveals a pattern that's broken elsewhere
- The area has accumulated similar issues
- A small refactor would prevent future bugs
- The narrow fix would be fragile

**3. Is there urgency that constrains options?**

If production is burning:
- Hotfix may be necessary
- But proper fix must follow
- See Hotfix Protocol below

### Scope Decision Output

```
## Scope Assessment

**Narrow fix viable?** [Yes/No]
- [Reasoning]

**Is narrow fix a hack?** [Yes/No]
- [Reasoning]

**Wider improvement warranted?** [Yes/No]
- [What improvement and why]

**Urgency level:** [Normal / Elevated / Critical]

**Recommended approach:** [Narrow Fix / Wider Improvement / Hotfix + Follow-up]
```

---

## Phase 3: Select Strategy

Based on Phase 2 assessment, choose one of three strategies:

### Strategy A: Narrow Fix

**When to use:**
- Root cause is isolated
- Fix doesn't require architectural changes
- Narrow fix addresses root cause (not a hack)
- No urgency forcing compromise

**Characteristics:**
- Minimal code change
- Low regression risk
- No follow-up required
- This is the default goal

### Strategy B: Wider Improvement

**When to use:**
- Narrow fix would be a hack
- Area needs improvement anyway
- Bug reveals a systemic issue
- Investment now prevents future bugs

**Requires explicit justification:**
```
## Wider Fix Justification

**Why narrow fix is insufficient:**
[Specific reasoning]

**Proposed improvement:**
[What you'll change beyond the minimal fix]

**Benefits:**
- [Benefit 1]
- [Benefit 2]

**Additional risk:**
- [What extra regression risk this introduces]

**Additional time:**
- [Estimate of extra time required]
```

**Get alignment before proceeding** — wider fixes should be agreed upon, not unilateral.

### Strategy C: Hotfix + Follow-up

**When to use:**
- Production impact is severe
- Proper fix requires more time than urgency allows
- A temporary fix can stabilize while proper fix is planned

**This strategy has strict requirements.** See Hotfix Protocol below.

---

## Phase 4: Execute

**Goal:** Implement the fix with minimal regression risk.

### Execution Principles

**1. Change only what's necessary**

For narrow fixes:
- Touch the minimum code required
- Resist "while I'm here" improvements
- Save improvements for separate PRs

For wider improvements:
- Stay within justified scope
- Don't expand beyond what was agreed

**2. Test the blast radius**

Run tests for:
- [ ] The specific bug (new test if none exists)
- [ ] Direct impact areas
- [ ] Indirect impact areas (integration points)

**3. Verify the fix**

- [ ] Bug no longer reproduces
- [ ] No new errors introduced
- [ ] Related functionality still works

**4. Document in code where needed**

If the fix isn't self-explanatory, add a comment explaining:
- What bug this fixes
- Why this approach was chosen (if not obvious)

---

## Phase 5: Documentation Check

**Goal:** Ensure Context Brain artifacts reflect any changes.

### When Documentation Updates Are Needed

| Situation | Action |
|-----------|--------|
| Component behavior changed | Update Component AGENTS.md |
| Pattern was wrong or misleading | Update Domain Guide |
| Architectural assumption was incorrect | Consider new ADR |
| Fix reveals undocumented gotcha | Add to relevant Domain Guide |
| Bug was caused by missing documentation | Add what was missing |

### Documentation Check Questions

1. **Did I change how a component behaves?**
   - If yes → Review its AGENTS.md, update if behavior/props/usage changed

2. **Did I discover a pattern was documented incorrectly?**
   - If yes → Update the Domain Guide (Law of Reciprocity)

3. **Did this bug reveal a decision that should be recorded?**
   - If yes → Consider an ADR for the fix approach

4. **Would this bug have been prevented by better documentation?**
   - If yes → Add the missing context now

### Documentation Output

```
## Documentation Status

**Updates needed:**
- [ ] [Document]: [What needs to change]

**No updates needed because:**
- [Reasoning if nothing needs to change]
```

---

## Hotfix Protocol

When urgency requires a temporary fix, follow this protocol strictly.

### Requirements (All Mandatory)

**1. Create follow-up ticket immediately**

Before merging the hotfix:
- Create ticket for proper fix
- Link it to the hotfix PR
- Set appropriate priority
- Include what the proper fix should address

**2. Add hotfix comment in code**

At the location of the temporary fix:

```
// HOTFIX: [TICKET-123] - [Brief description of bug]
// 
// Temporary fix: [What this code does]
// Why temporary: [Why proper fix couldn't be done now]
// Proper fix: [What the real fix should be]
// Follow-up ticket: [TICKET-456]
// 
// TODO: Remove this hotfix when [TICKET-456] is completed
```

**3. Set timeline expectation**

Hotfixes should be replaced within:
- Critical bugs: 1-2 sprints
- Major bugs: 1 month
- Minor bugs: 1 quarter

If a hotfix lives longer, it becomes permanent tech debt.

### Hotfix Anti-Abuse

This protocol exists because hotfixes are sometimes necessary, not because they're desirable.

**Red flags that hotfix is being abused:**
- Multiple hotfixes in the same area
- Hotfixes without follow-up tickets
- Follow-up tickets that never get prioritized
- "Temporary" fixes that are months old

**If you notice these patterns, raise them.** Accumulated hotfixes signal a systemic problem.

---

## Anti-Patterns

Recognize and avoid these bug-fixing failures:

| Anti-Pattern | What It Looks Like | Why It's Wrong |
|--------------|-------------------|----------------|
| **Symptom Fixing** | Fix masks the problem without addressing cause | Bug will resurface or appear elsewhere |
| **Shotgun Debugging** | Change random things until it works | Creates new bugs, no understanding |
| **Gold Plating** | "While I'm here" refactors beyond bug scope | Scope creep, unnecessary risk |
| **Untested Fixes** | "It works on my machine" | Regression waiting to happen |
| **Permanent Hotfixes** | Temporary fix becomes permanent | Tech debt accumulation |
| **Silent Fixes** | No documentation of what changed or why | Future maintainers left confused |
| **Blame Fixing** | Defensive code that checks for "impossible" states | Masks real bugs upstream |

---

## Quick Reference

### The 5 Phases

1. **Diagnose** → Reproduce, root cause, blast radius
2. **Assess** → Narrow viable? Is it a hack? Wider needed?
3. **Select** → Narrow / Wider / Hotfix+Follow-up
4. **Execute** → Minimal change, test blast radius, verify
5. **Document** → Context Brain check

### Strategy Selection Shortcut

```
Can narrow fix address root cause without being a hack?
├─► YES → Narrow Fix
└─► NO → Is there time to do it right?
         ├─► YES → Wider Improvement (with justification)
         └─► NO → Hotfix + Follow-up (with protocol)
```

### Hotfix Requirements

1. Follow-up ticket created
2. Code comment with HOTFIX marker
3. Timeline for proper fix

### Documentation Triggers

- Behavior changed → AGENTS.md
- Pattern was wrong → Domain Guide
- New architectural insight → Consider ADR
- Missing context caused bug → Add it now
