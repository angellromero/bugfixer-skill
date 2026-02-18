# Fix Strategy Decision Guide

This reference provides detailed guidance on choosing between narrow fixes, wider improvements, and hotfixes. Consult when the right approach isn't immediately clear.

---

## The Core Tension

Every bug fix involves a tradeoff:

| Fix Too Narrow | Fix Too Wide |
|----------------|--------------|
| Doesn't address root cause | Addresses more than necessary |
| Creates tech debt | Creates unnecessary risk |
| Bug may resurface | Longer timeline |
| "Hack" territory | "Scope creep" territory |

The goal is the **minimal correct fix** — the smallest change that fully addresses the root cause without being a hack.

---

## Strategy A: Narrow Fix (Deep Dive)

### When It's Right

A narrow fix is the correct choice when:

1. **Root cause is contained**
   - The bug is caused by a specific, isolated issue
   - Fixing that issue doesn't require touching other code
   - The fix doesn't depend on changes elsewhere

2. **Existing patterns are sound**
   - The surrounding code follows good patterns
   - This bug is an anomaly, not a symptom of broader issues
   - The pattern doesn't need rethinking

3. **Fix is sustainable**
   - The fix will hold up over time
   - It doesn't create fragile special cases
   - Future developers won't be confused by it

### When Narrow Becomes a Hack

**Warning signs that your narrow fix is actually a hack:**

| Sign | Example |
|------|---------|
| Special case for one scenario | `if (userId === 'user_123') { /* workaround */ }` |
| Checking for "impossible" states | `if (data && data.id && data.id !== null && ...)` |
| Contradicting design intent | Adding sync code to an async flow |
| "This shouldn't happen but..." comments | Defensive code masking upstream bugs |
| Duplicating logic to avoid touching shared code | Copy-paste instead of fixing the source |
| Magic numbers or hardcoded values | `setTimeout(() => retry(), 1500) // seems to work` |

**If you see these signs, reassess.** A hack isn't a fix — it's deferred pain.

### Narrow Fix Checklist

Before committing a narrow fix, verify:

- [ ] This addresses the root cause, not just the symptom
- [ ] I'm not adding special-case logic that shouldn't exist
- [ ] The fix follows existing patterns in this area
- [ ] This won't need to be revisited in the near future
- [ ] Future developers will understand why this fix exists

---

## Strategy B: Wider Improvement (Deep Dive)

### When It's Right

A wider improvement is warranted when:

1. **Narrow fix would be a hack**
   - The minimal change would violate patterns
   - It would create a fragile special case
   - It contradicts the design intent

2. **Bug reveals systemic issue**
   - The same bug exists or could exist elsewhere
   - The pattern that caused this bug is used in other places
   - Fixing one instance would leave time bombs

3. **Area is already problematic**
   - This code has had multiple bugs before
   - Developers frequently struggle with this area
   - A small investment now prevents larger costs later

4. **Improvement is bounded**
   - You can clearly define the scope
   - It's not a full rewrite
   - The risk is manageable

### Justification Requirements

Wider fixes require explicit justification because they carry more risk. Before proceeding:

**1. Explain why narrow is insufficient**
```
The narrow fix would require adding a special check for [condition], 
which contradicts the [pattern name] we use elsewhere. This would 
create confusion and likely cause similar bugs when others follow 
the inconsistent pattern.
```

**2. Bound the scope**
```
The wider fix includes:
- Refactoring [specific function/component]
- Updating [N] call sites
- Does NOT include [explicitly excluded items]
```

**3. Quantify the cost**
```
Additional time: ~[X] hours
Additional files changed: [N]
Additional test coverage needed: [describe]
```

**4. Get alignment**

Don't unilaterally expand scope. Communicate with:
- Ticket reporter (if timeline changes)
- Team lead (if scope significantly expands)
- Code owners (if touching shared code)

### Wider Fix Boundaries

Even when wider improvement is warranted, stay disciplined:

**DO:**
- Fix the bug and directly related issues
- Improve code that caused or contributed to the bug
- Update documentation for changed behavior
- Add tests for the improved code

**DON'T:**
- Refactor unrelated code "while you're here"
- Upgrade dependencies unless necessary for the fix
- Rename things for consistency across the codebase
- Address tech debt in other areas

**Rule of thumb:** If someone asked "why is this change in a bug fix PR?", you should have a direct answer connecting it to the bug.

### Wider Fix Checklist

Before committing a wider fix, verify:

- [ ] Narrow fix was genuinely insufficient (not just less elegant)
- [ ] Scope is explicitly defined and bounded
- [ ] Stakeholders are aligned on expanded scope
- [ ] All changes directly relate to the bug
- [ ] Risk is proportionate to the benefit
- [ ] Timeline impact is communicated

---

## Strategy C: Hotfix + Follow-up (Deep Dive)

### When It's Right

Hotfix approach is appropriate when:

1. **Urgency is real**
   - Production is significantly impacted
   - Users are actively experiencing problems
   - Business operations are affected

2. **Proper fix requires significant time**
   - Root cause is understood but fix is complex
   - Proper fix needs careful planning
   - Testing requirements are extensive

3. **A stable temporary state exists**
   - You can stop the bleeding without causing new issues
   - The temporary fix is genuinely temporary (not permanent)
   - The hotfix doesn't make the proper fix harder

### When Hotfix is NOT Appropriate

Don't use hotfix as an excuse for:

| Situation | Do Instead |
|-----------|------------|
| "I don't want to do the full fix" | Do the full fix |
| Moderate priority bug | Normal fix with normal timeline |
| Unclear root cause | More diagnosis, not guessing |
| "We'll get to it later" attitude | Proper fix now if possible |

### The Hotfix Contract

When you ship a hotfix, you're making promises:

1. **"I will create a follow-up ticket"**
   - Not optional
   - Must happen before merge
   - Must include proper fix description

2. **"I will document this in code"**
   - HOTFIX comment with full context
   - Link to both bug and follow-up tickets
   - Describe what proper fix should be

3. **"This will be temporary"**
   - Define timeline for proper fix
   - Escalate if timeline slips
   - Don't let it become permanent

### Hotfix Comment Format

```javascript
// HOTFIX: [BUG-123] - Brief description of the bug
// 
// Temporary fix applied: [What this code does as a workaround]
// 
// Why this is temporary:
// - [Reason 1 why this isn't the real fix]
// - [Reason 2]
// 
// Proper fix should:
// - [What the real fix needs to do]
// - [Additional requirements]
// 
// Follow-up ticket: [TICKET-456]
// Expected resolution: [Sprint X / Date / Timeline]
// 
// DO NOT modify this hotfix without addressing the follow-up ticket.
```

### Monitoring Hotfix Health

Track hotfixes to prevent abuse:

**Healthy indicators:**
- Follow-up tickets exist for all hotfixes
- Hotfixes are resolved within stated timelines
- Hotfix count is low and stable

**Unhealthy indicators:**
- Hotfixes without follow-up tickets
- Follow-up tickets deprioritized repeatedly
- Growing number of active hotfixes
- Hotfixes older than one quarter

**If unhealthy patterns emerge:** Raise to leadership. This is a systemic prioritization problem, not an individual discipline problem.

---

## Decision Tree

Use this when you're unsure which strategy to select:

```
START: Bug diagnosed, root cause understood
│
├─► Can the bug be fixed with a narrow change?
│   │
│   ├─► YES: Is that narrow change a hack?
│   │        │
│   │        ├─► NO → STRATEGY A: Narrow Fix
│   │        │
│   │        └─► YES → Does surrounding area need improvement?
│   │                  │
│   │                  ├─► YES → Go to "Wider Improvement" below
│   │                  │
│   │                  └─► NO → Reconsider. If narrow is a hack but
│   │                           wider isn't warranted, something is wrong.
│   │                           Return to diagnosis.
│   │
│   └─► NO: Is there time to implement the proper fix?
│            │
│            ├─► YES → STRATEGY B: Wider Improvement
│            │
│            └─► NO → Is production severely impacted?
│                      │
│                      ├─► YES → STRATEGY C: Hotfix + Follow-up
│                      │
│                      └─► NO → Push back on timeline. 
│                               Rushing creates more bugs.
```

---

## Example Scenarios

### Scenario 1: Clear Narrow Fix

**Bug:** Button doesn't submit form when clicked
**Root Cause:** Event handler was `onClick` instead of `onSubmit`
**Blast Radius:** Just this component

**Analysis:**
- Fix is narrow (one line change)
- Fix addresses root cause directly
- Not a hack — follows standard patterns
- Surrounding code is fine

**Decision:** Strategy A — Narrow Fix

---

### Scenario 2: Narrow Fix Would Be a Hack

**Bug:** User's cart total sometimes shows wrong price
**Root Cause:** Race condition when multiple price updates happen
**Narrow "Fix":** Add setTimeout to debounce updates

**Analysis:**
- setTimeout is a hack masking the real issue
- The real issue is the state management pattern
- This pattern is used elsewhere and will cause similar bugs

**Decision:** Strategy B — Wider Improvement
- Refactor cart state to handle concurrent updates properly
- Apply the pattern to other affected areas

---

### Scenario 3: Hotfix Necessary

**Bug:** Checkout completely broken for 30% of users
**Root Cause:** Payment API contract changed, full integration needs update
**Urgency:** Revenue actively lost

**Analysis:**
- Production is burning
- Proper fix is 2-3 days of work
- Temporary workaround can restore function

**Decision:** Strategy C — Hotfix + Follow-up
- Hotfix: Catch the new error format, convert to old format
- Follow-up: Full integration update with proper error handling
- Timeline: Proper fix in next sprint

---

### Scenario 4: Hotfix Temptation to Resist

**Bug:** Report generation slow for large datasets
**"Urgency":** Customer complained, PM wants it fixed today
**Proposed "Hotfix":** Add caching that might have stale data issues

**Analysis:**
- Bug is annoying but not breaking
- "Hotfix" would introduce new bugs
- Proper fix is 1 day of work

**Decision:** Push back. This is NOT a hotfix scenario.
- Do the proper fix with correct caching invalidation
- Communicate realistic timeline
- Don't create a worse bug to fix a minor one
