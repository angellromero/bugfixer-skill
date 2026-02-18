# Bug Fixer Skill

An Agent Skill for strategic bug fixing with minimal regression risk.

## Overview

Bug Fixer guides AI agents through a disciplined approach to diagnosing and resolving defects. It ensures fixes address root causes (not just symptoms), stay within appropriate scope, and keep documentation current.

**Use this skill when:**
- Working on bug tickets or defect reports
- Investigating unexpected behavior
- Fixing production issues
- Troubleshooting test failures

## Key Principles

- **Understand before acting** — Reproduce and diagnose before writing fix code
- **Find the middle path** — Neither too narrow (hack) nor too wide (scope creep)
- **Minimize regression risk** — Map blast radius, test affected areas
- **Document changes** — Update relevant documentation when behavior changes

## The 5-Phase Process

```
1. DIAGNOSE    → Reproduce, identify root cause, map blast radius
2. ASSESS      → Determine if narrow fix is viable or a hack
3. SELECT      → Choose: Narrow Fix / Wider Improvement / Hotfix
4. EXECUTE     → Implement with minimal change, verify fix
5. DOCUMENT    → Update Context Brain artifacts if needed
```

## Fix Strategies

| Strategy | When to Use |
|----------|-------------|
| **Narrow Fix** | Root cause is isolated, fix isn't a hack, low risk |
| **Wider Improvement** | Narrow fix would be a hack, or area needs improvement |
| **Hotfix + Follow-up** | Production is burning, proper fix must follow |

## Features

- **Root Cause Analysis** — Guides asking "why" until the true cause is found
- **Scope Assessment** — Prevents both hacks and scope creep
- **Blast Radius Mapping** — Identifies what could be affected by changes
- **Hotfix Protocol** — Strict requirements for temporary fixes (follow-up ticket, code comments, timeline)
- **Documentation Triggers** — Prompts to update AGENTS.md, Domain Guides, or ADRs when appropriate
- **Anti-Pattern Detection** — Identifies symptom fixing, shotgun debugging, gold plating, and more

## Installation

Copy `SKILL.md` to your Cursor skills directory:

```
~/.cursor/skills/bugfixer-skill/SKILL.md
```

Or add it to a project-specific `.cursor/skills/` folder.

## Integration

This skill integrates with **Context Brain** for documentation updates. When a fix changes component behavior or reveals incorrect patterns, the skill prompts appropriate documentation updates.

## Quick Reference

### Strategy Selection

```
Can narrow fix address root cause without being a hack?
├─► YES → Narrow Fix
└─► NO → Is there time to do it right?
         ├─► YES → Wider Improvement (with justification)
         └─► NO → Hotfix + Follow-up (with protocol)
```

### Hotfix Requirements

1. Follow-up ticket created before merging
2. Code comment with `HOTFIX:` marker
3. Timeline for proper fix (1-2 sprints for critical, 1 month for major)

### Documentation Triggers

| Change | Update |
|--------|--------|
| Behavior changed | Component AGENTS.md |
| Pattern was wrong | Domain Guide |
| New architectural insight | Consider ADR |
| Missing context caused bug | Add it now |

## License

MIT
