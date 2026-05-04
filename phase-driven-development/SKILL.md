---
name: phase-driven-development
description: Use when a feature qualifies for phasing — 2+ new persistence schemas (tables, collections, stores), 2+ new user-facing surfaces (pages, endpoints, screens, CLI commands), background work (queue jobs, workers, cron, scheduled tasks), spans multiple modules/domains, or has a natural multi-phase rollout. Stack-agnostic. Activate before any planning, coding, or file creation begins.
---

# Phase-Driven Development

## Overview

Complex features are built in phases. Each phase follows a full brainstorm → spec → plan → implement cycle. Nothing is implemented until the user approves the plan.

**Core principle:** Show before you do. Every phase needs explicit user approval before a single line of code is written.

## When to Use

A feature qualifies for phasing if it meets **2 or more** of:

- 2+ new persistence schemas (DB tables, collections, key-value stores, files)
- 2+ new user-facing surfaces (HTTP endpoints, pages, screens, CLI commands, public functions)
- Background work (queue jobs, workers, cron, scheduled tasks, daemons)
- Spans multiple modules, services, or domains
- Has a natural multi-phase rollout (e.g. backend first, then UI; data layer first, then API)
- Make sure to ask the user for permission before applying this skill

If unsure, **ask user wether to make the development phase driven**.

## The Cycle (per phase)

1. **Brainstorm** — explore design space, identify open questions, surface tradeoffs
2. **Spec** — write detailed design doc → `docs/superpowers/specs/<date>-<topic>-design.md` (superpowers convention; append `-phase<N>` to `<topic>` if needed for disambiguation)
3. **Plan** — write step-by-step implementation plan → `docs/superpowers/plans/<date>-<feature>.md` (append `-phase<N>` to `<feature>` if needed)
4. **Show user** — present the plan and **wait for explicit approval**
5. **Implement** — execute plan task by task (TDD, commit per task)

## Phase Sizing & Done

### Sizing

A phase is a **shippable slice**. Smaller is better — fast feedback, cheap rework.

- **Tasks:** ~8–10 plan tasks (a task = one TDD test→impl→commit step)
- **Scope:** one coherent goal, one PR
- **Independence:** ships to main without breaking other in-flight phases
- **Reviewability:** human reviewer holds the diff in head; plan fits in one approval-ask message

If a phase exceeds 10 tasks, split it. If a phase has fewer than 3 tasks, fold it into an adjacent phase with shared context.

### Done

Phase status = `merged` only when **all** hold:

- [ ] All plan tasks checked off in the todos file
- [ ] Acceptance criteria from the spec observable (run check, paste evidence)
- [ ] PR merged to main (or equivalent integration target)
- [ ] Roadmap row updated: `Status: merged`, PR link, `Updated:` set
- [ ] Todos file frozen (no further edits)
- [ ] Next phase's dependencies (if any) now satisfied

Anything short of all six = phase **not** done. Do not start Phase N+1.

## Mid-Phase Scope Changes

Scope grows mid-implementation? Stop. Don't silently expand.

Classify the change:

| Type | Action |
|------|--------|
| Bug in plan (wrong path, missing dep, broken test) | Fix inline, note in todos "Deviations from Plan" |
| New task within phase scope (≤2 added tasks, same goal, acceptance criteria unchanged) | Append to plan + todos, note deviation, continue |
| New scope (changes goal, breaks acceptance criteria, >2 added tasks) | **Stop. Re-spec. Re-approve.** |
| Separate concern (different goal entirely) | Defer to new phase in roadmap. Don't bundle. |

### Re-approve Flow (when triggered)

1. Pause implementation. Commit work-in-progress.
2. Update spec with new scope. Set roadmap status back to `spec` or `plan`.
3. Update plan with new/changed tasks.
4. Re-run approval gate (use Approval Ask template — flag as re-approval).
5. Wait for explicit yes. Resume only after.

**Red flag:** "I'll just add this one thing" while implementing. That sentence = stop, classify.

## Skill Interop

This skill orchestrates the phase loop. It delegates each step to a focused superpowers skill. If superpowers unavailable, fall back to manual execution of the same step.

| Cycle Step | Skill | Output |
|------------|-------|--------|
| 1. Brainstorm | `superpowers:brainstorming` | Spec at `docs/superpowers/specs/<date>-<topic>-design.md` |
| 2. Spec | (produced by brainstorming) | — |
| 3. Plan | `superpowers:writing-plans` | Plan at `docs/superpowers/plans/<date>-<feature>.md` |
| 4. Approval gate | **this skill** | User says yes |
| 5. Implement (parallel tasks) | `superpowers:subagent-driven-development` | Commits per task |
| 5. Implement (inline) | `superpowers:executing-plans` | Commits per task |
| 5a. Each task | `superpowers:test-driven-development` | Test → impl → commit |
| 6. Verify before claiming done | `superpowers:verification-before-completion` | Evidence of pass |
| 7. Finish phase | `superpowers:finishing-a-development-branch` | Merge / PR / cleanup |

**On failure during implementation:** `superpowers:systematic-debugging` before retry.

### Bookkeeping Ownership

Delegated skills do **not** know about this skill's roadmap or per-phase todos file. They only produce their own artifacts (spec, plan, commits, PR). Phase-driven-development is the orchestrator and owns the bookkeeping:

- After a delegated skill returns, **this skill** updates the roadmap row (status, links) and the per-phase todos file (check off tasks, append discoveries/blockers).
- Update happens **at every transition**, even when superpowers handled the actual work.
- Announce handoffs: "Using `<skill>` to <purpose>." On return: "Updating roadmap row / todos."

**Fallback (no superpowers):** use the templates in this skill, run TDD manually, verify before claiming done. Bookkeeping rule unchanged — update roadmap/todos at each transition.

## Progress Tracking (Roadmap as State File)

The roadmap doc is the **single source of truth for phase progress**. Survives sessions. Resume from it.

Path: `docs/roadmaps/<feature>-roadmap.md`

Each phase has a row with status:

| Status | Meaning |
|--------|---------|
| `planned` | Listed in roadmap, no spec yet |
| `brainstorming` | Exploring design, no spec yet |
| `spec` | Spec written, plan not yet |
| `plan` | Plan written, awaiting user approval |
| `approved` | User approved, implementation in progress |
| `in-review` | PR open |
| `merged` | Landed on main, phase done |
| `blocked` | Waiting on external (note reason) |

Required columns: **Phase**, **Status**, **Spec**, **Plan**, **Todos**, **PR**, **Updated**.

Update the row immediately on every transition. Commit roadmap changes alongside phase work.

## Per-Phase Todos (Granular Execution Log)

Plan = intent (immutable after approval). Todos = live execution log (mutable). Native session task tools are ephemeral; this file persists across sessions.

Path: `docs/roadmaps/<feature>/phase<N>-todos.md`

Lifecycle:
- Created when phase enters `approved`
- Seeded from plan's task list as checkboxes
- Updated live: check off completed tasks, append discovered sub-tasks, note blockers/decisions
- Frozen at phase merge (kept for retrospective)

Format: see Templates section.

Sync with native session task tools: at session start, mirror unchecked file todos into the session task list for in-conversation tracking. File = persistent truth. Session tasks = ephemeral view.

## Resuming Work

On session start (or when picking up a feature):

1. Read `docs/roadmaps/<feature>-roadmap.md`
2. Find first non-`merged` phase
3. Resume from its current status:
   - `planned` → brainstorm
   - `brainstorming` → write spec
   - `spec` → write plan
   - `plan` → present approval gate
   - `approved` → read `phase<N>-todos.md`, continue from first unchecked
   - `in-review` → address PR feedback
   - `blocked` → surface blocker to user
4. If status is `approved` but todos file is missing, create it from the plan before continuing
5. Never skip ahead — finish current phase before starting next

## Rules

- Track all phases in `docs/roadmaps/<feature>-roadmap.md` (see Progress Tracking)
- Update phase status on every transition; the roadmap row is the resumable state
- Each phase has its own brainstorm → spec → plan → implement cycle
- **Do not write any code until the user approves the plan for that phase**
- Create the per-phase todos file when phase enters `approved`; update on every task transition
- One phase at a time — do not start Phase N+1 until Phase N is merged to main
- Phases that depend on earlier ones must wait for the dependency to land on main

## The Approval Gate

After writing the plan, you MUST:

1. Post the **Approval Ask** message (see Templates → Approval Ask) — includes scope, risks, out-of-scope, explicit ask line
2. Wait for explicit confirmation — "yes", "go ahead", "proceed", or similar
3. Only then begin implementation; transition roadmap row to `approved`

**No implicit approval.** User reading the plan is not approval. Silence is not approval.

## Templates

Drop-in starting points. Adjust paths/columns to project conventions but keep structure.

### Spec & Plan — Defer to Superpowers

If superpowers skills are available, use them as the source of truth:
- Spec → `superpowers:brainstorming` writes to `docs/superpowers/specs/<date>-<topic>-design.md`
- Plan → `superpowers:writing-plans` writes to `docs/superpowers/plans/<date>-<feature>.md`

This skill links to those artifacts in the roadmap row. Do not duplicate or override their formats.

**Fallback** (only if superpowers unavailable):

```markdown
# Spec: <feature> Phase <N>
## Context / Goals / Non-Goals / Design / Open Questions / Risks / Acceptance
```

```markdown
# Plan: <feature> Phase <N>
## Summary
## Tasks (numbered; each: Files, Change, Test, Acceptance, Commit)
## Verification / Rollback
```

### Roadmap — `docs/roadmaps/<feature>-roadmap.md`

```markdown
# <Feature> Roadmap

**Goal:** <one sentence>
**Owner:** <user/team>  **Started:** <YYYY-MM-DD>

## Phases

| Phase | Title | Status | Spec | Plan | Todos | PR | Updated |
|-------|-------|--------|------|------|-------|----|---------|
| 1 | <title> | planned | — | — | — | — | <YYYY-MM-DD> |
| 2 | <title> | planned | — | — | — | — | <YYYY-MM-DD> |

## Phase Notes

### Phase 1 — <title>
<2–4 sentences: scope, why first, dependencies>

## Out of Scope
- <not covered by any phase>
```

### Per-Phase Todos — `docs/roadmaps/<feature>/phase<N>-todos.md`

```markdown
# Phase <N> Todos — <feature>

Plan: <link>  Status: in-progress  Updated: <YYYY-MM-DD>

## Tasks (from plan)
- [x] Task 1 — <commit-sha>
- [ ] Task 2
  - [x] sub-step (discovered mid-work)
  - [ ] sub-step
- [ ] Task 3 — BLOCKED: <reason>

## Discoveries / Decisions
- <YYYY-MM-DD>: <note>

## Deviations from Plan
- <task>: <what changed and why>
```

### Approval Ask (post in chat after plan written)

```
Phase <N> plan ready: <link>

Scope:
- <task 1 one-liner>
- <task 2 one-liner>

Risks: <one line or "none">
Out of scope: <one line>

Ready to proceed with Phase <N> implementation? (yes / changes / no)
```

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Start coding during brainstorm | Stop. Write spec first. |
| Commit changes before showing plan | Never commit before approval. Show diff, wait. |
| Skip roadmap doc | Create it before any other docs or code |
| Treat user silence as approval | Ask explicitly, wait for a clear yes |
| Start Phase 2 while Phase 1 is in review | Wait for merge to main |
| "Small enough to skip the process" | 2+ qualifying criteria = full cycle, no exceptions |

## Red Flags — STOP

- About to write code without a user-approved plan → STOP
- About to commit anything plan-related without showing it first → STOP
- Starting Phase N+1 while Phase N hasn't merged → STOP
- "This part is simple, I'll just do it" → STOP

All of these mean: go back to the cycle. Show the user. Get approval.
