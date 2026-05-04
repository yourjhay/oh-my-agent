---
name: pdd
description: >-
  PDD — Phase-Driven Development. Use when a feature qualifies for phasing
  (2+ new persistence surfaces, 2+ new user-facing surfaces, background work,
  multi-domain, or natural multi-phase rollout) and you want a roadmap-first
  workflow — brainstorm a phased roadmap, explicit roadmap approval,
  then per-phase brainstorm → spec → writing-plans → implement. Delegates to
  superpowers skills. Stack-agnostic. Activate before planning or coding.
---

# PDD — Phase-Driven Development

## Overview

PDD splits large work into **phases**. First pass produces only a **roadmap** doc (Phase 0). You **must** get **roadmap OK** from the user before writing any Phase 1 spec. Each phase then runs the normal superpowers loop: **brainstorming → spec → writing-plans → approval → implement → merge**, updating the **roadmap status table** as you go.

**Core ideas:** Roadmap before specs; **no** whole-feature `writing-plans` after the roadmap brainstorm; **explicit** gates; **G7:** keep `docs/roadmaps/<feature>-roadmap.md` status rows truthful through each transition and **after every phase merge**.

**Optional progress files:** The user may opt in to per-phase `phase-<N>-todos.md` files (see below) for a live implementation checklist. **Before** you run Phase 0 brainstorming, **ask** whether they want this; record the answer on the roadmap so later work does not create todo files if they declined.

## When to use

Use when **two or more** of these apply:

- 2+ new persistence shapes (tables, collections, stores, files)
- 2+ new user-facing surfaces (APIs, pages, screens, CLI)
- Background work (queues, workers, cron)
- Spans multiple modules or domains
- Natural phased rollout (e.g. data → API → UI)

If unclear, **ask** whether to use PDD. Do **not** skip this skill when criteria apply.

## State machine

1. **Qualify** — Confirm phasing fit; invoke PDD.
2. **Progress-tracking preference (before roadmap)** — Ask the **per-phase todos** question verbatim ([`reference.md`](reference.md)). **Wait** for the answer, then run Phase 0 with that choice recorded on the roadmap.
3. **Phase 0 — Roadmap** — `superpowers:brainstorming` with **Phase 0 contract** ([`reference.md`](reference.md)): output **only** `docs/roadmaps/<feature>-roadmap.md`, including the **Progress tracking** line (enabled or declined). **Do not** invoke `writing-plans` for the entire feature here. **Do not** write per-phase design specs yet.
4. **Roadmap OK gate** — Present roadmap path + summary. Ask the **roadmap OK** question verbatim ([`reference.md`](reference.md)). **Wait.** No Phase 1 spec work until the user approves (or you revise and re-ask).
5. **For each phase (sequential)**  
   - **Spec:** `superpowers:brainstorming` for **this phase only** → `docs/superpowers/specs/<date>-<topic>-design.md` (use `-phase<N>` in `<topic>` when useful). Optional visuals **only** if the user requested ([`reference.md`](reference.md)).  
   - **Spec review** — Per brainstorming: user reviews the spec file; revise until approved.  
   - **Plan:** `superpowers:writing-plans` → `docs/superpowers/plans/<date>-<feature>.md` (add `-phase<N>` when useful).  
   - **Plan approval** — Post Approval Ask ([`reference.md`](reference.md)); wait for explicit yes.  
   - **Optional todos file** — If progress tracking is **enabled** on the roadmap: after the user approves the phase plan, create or refresh `docs/roadmaps/<feature>/phase-<N>-todos.md` — **sections and tasks mirror the approved plan** (same order and granularity as the plan’s implementation tasks); use checkboxes and brief notes; **update during implementation** (check off, note blockers). If progress tracking is **declined** on the roadmap, do **not** create `phase-*-todos.md`.  
   - **Implement** — TDD / `executing-plans` / `subagent-driven-development` per project norms; keep `phase-<N>-todos.md` in sync if enabled.  
   - **Verify** — `verification-before-completion, requesting-code-review ` before claiming done.  
   - **Merge** — `finishing-a-development-branch`; then **immediately** update the roadmap row for this phase (**merged**, PR link if column exists, `Updated`, spec/plan links).  
   - **Advance roadmap row during the phase** — When spec exists, plan exists, user approved plan, PR open — set Status per [`reference.md`](reference.md) transition rules.
6. **Next phase** — Only after current phase is **merged** and roadmap reflects it.

On failure while implementing: **`systematic-debugging`** before guessing fixes.

## Skill interop

| Step | Skill | Output / owner |
|------|--------|----------------|
| Progress preference | **PDD** | Ask before Phase 0; record on roadmap |
| Roadmap | `superpowers:brainstorming` (Phase 0) | `docs/roadmaps/<feature>-roadmap.md` |
| Roadmap gate | **PDD** | User says roadmap OK |
| Phase spec | `superpowers:brainstorming` | `docs/superpowers/specs/...-design.md` |
| Phase plan | `superpowers:writing-plans` | `docs/superpowers/plans/...md` |
| Phase todos (optional) | **PDD** | `docs/roadmaps/<feature>/phase-<N>-todos.md` if enabled |
| Implement | TDD / `executing-plans` / `subagent-driven-development` | Code |
| Verify | `verification-before-completion, requesting-code-review ` | Evidence |
| Merge | `finishing-a-development-branch` | PR merged |
| Roadmap status | **PDD (required)** | Update roadmap file on transitions + after each merge |
| Debug | `systematic-debugging` | — |

## Rules (checklist)

- [ ] **Before Phase 0:** asked whether to use per-phase `phase-<N>-todos.md` progress files; roadmap records **Progress tracking: enabled** or **declined**.
- [ ] Phase 0 delivers **roadmap file only** — not the whole-feature spec, not `writing-plans` for the whole feature.
- [ ] **Roadmap OK** received before Phase 1 spec brainstorming.
- [ ] **Roadmap file** status table updated when phase advances (`spec` → `plan` → `approved` → `in-review` → `merged`) and **after each merge**.
- [ ] **One phase at a time** — next phase only after merge + roadmap updated.
- [ ] **No code** until user approves the **phase plan** (not just “read the plan”).
- [ ] Optional **visuals** only when the user asked.
- [ ] If progress tracking **enabled:** each phase has `phase-<N>-todos.md` aligned to that phase’s **plan**, updated during implementation; if **declined**, no `phase-*-todos.md` files.

## What lives in `reference.md`

Read it whenever executing PDD:

- Phase 0 brainstorming **contract** (paste / follow).
- **Roadmap OK** and **plan approval** copy-paste lines.
- **Status** values and **transition rules**.
- Roadmap document **shape** and **template slot** (you may replace with your full table later).
- Per-phase brainstorming **preamble**.
- Optional **visuals** paths.
- **Mid-phase scope** classification (full table in [`reference.md`](reference.md)).
- **Progress tracking:** verbatim pre-roadmap question, roadmap **Progress tracking** line, and `phase-<N>-todos.md` shape (when enabled).

Per-phase todo files are **optional** and **user-gated** before the roadmap. If the user declines, the roadmap states that and agents **must not** add `phase-*-todos.md`. Otherwise use your usual tracker.
