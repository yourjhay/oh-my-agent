---
name: phase-driven-development
description: >-
  PDD — Phase-Driven Development. Use when a feature qualifies for phasing
  (2+ new persistence surfaces, 2+ new user-facing surfaces, background work,
  multi-domain, or natural multi-phase rollout) and you want a roadmap-first
  workflow — brainstorm a phased roadmap, explicit roadmap approval,
  then per-phase brainstorm → spec → writing-plans → implement. Delegates to
  superpowers skills. Stack-agnostic. Skill folder: `skills/phase-driven-development/`.
  Activate before planning or coding when criteria match; see When NOT to use.
---

# PDD — Phase-Driven Development

## Overview

PDD splits large work into **phases**. First pass produces only a **roadmap** doc (Phase 0). You **must** get **roadmap approval** from the user before writing any Phase 1 spec. Each phase then runs the normal superpowers loop: **brainstorming → spec → writing-plans → approval → implement → merge**, updating the **roadmap status table** as you go.

**Core ideas:** Roadmap before specs; **no** whole-feature `writing-plans` after the roadmap brainstorm; **explicit** gates; **G7:** keep `docs/roadmaps/<feature>-roadmap.md` status rows truthful through each transition and **after every phase merge**.

**Optional progress files:** The user may opt in to per-phase `phase-<N>-todos.md` files (see below) for a live implementation checklist. **Before** you run Phase 0 brainstorming, **ask** whether they want this; record the answer on the roadmap so later work does not create todo files if they declined.

**Before Phase 0 and whenever updating roadmap status:** read [`reference.md`](reference.md) for verbatim prompts, G7 transitions, roadmap template, and **mid-phase scope** rules.

## When to use

Use when **two or more** of these apply:

- 2+ new persistence shapes (tables, collections, stores, files)
- 2+ new user-facing surfaces (APIs, pages, screens, CLI)
- Background work (queues, workers, cron)
- Spans multiple modules or domains
- Natural phased rollout (e.g. data → API → UI)

If criteria are borderline, **ask** once: use PDD or a single-phase flow. Do **not** skip this skill when criteria clearly apply.

## When NOT to use

Do **not** invoke PDD for:

- Single persistence or single user-facing surface with no natural phased rollout
- Bugfix, copy, or config-only change with no new architecture
- Docs-only or rename-only work (unless paired with qualifying criteria above)
- User explicitly requests a **one-shot** plan and the work fits **one** phase — use normal superpowers flow without PDD

## Naming conventions (normative)

| Token | Rule |
|-------|------|
| `<feature>` | Lowercase **kebab-case** slug; must match `docs/roadmaps/<feature>-roadmap.md` basename (without `-roadmap`) |
| `<N>` | Phase index as in the roadmap table (typically starting at `1`) |
| `<date>` in filenames | ISO `YYYY-MM-DD` |
| `<topic>` in spec/plan filenames | Include `-phase<N>` when multiple phases could collide |

## Canonical paths

| Artifact | Path |
|----------|------|
| Roadmap | `docs/roadmaps/<feature>-roadmap.md` |
| Phase spec | `docs/superpowers/specs/<date>-<topic>-design.md` |
| Phase plan | `docs/superpowers/plans/<date>-<topic>.md` |
| Phase todos (optional) | `docs/roadmaps/<feature>/phase-<N>-todos.md` |

If `docs/roadmaps/` is missing, **create** it before writing the roadmap.

## State machine

1. **Qualify** — Confirm PDD applies (**When to use** / **When NOT to use**).
2. **Progress-tracking preference (before roadmap)** — Ask the **per-phase todos** question verbatim ([`reference.md`](reference.md)). **Wait** for `yes` or `no` (case-insensitive). Then run Phase 0 with that choice recorded on the roadmap.
3. **Phase 0 — Roadmap** — `superpowers:brainstorming` with **Phase 0 contract** ([`reference.md`](reference.md)): output **only** `docs/roadmaps/<feature>-roadmap.md`, including the **Progress tracking** line (enabled or declined). **Do not** invoke `writing-plans` for the entire feature here. **Do not** write per-phase design specs yet.
4. **Roadmap OK gate** — Present roadmap path + summary. Ask the **roadmap OK** question verbatim ([`reference.md`](reference.md)). **Wait.** Treat approval per **Gate equivalence** in [`reference.md`](reference.md). No Phase 1 spec work until approved (or you revise, re-ask, and user approves).
5. **For each phase (sequential)**  
   - **Spec:** `superpowers:brainstorming` for **this phase only** → spec path under **Canonical paths**. Optional visuals **only** if the user requested ([`reference.md`](reference.md)).  
   - **Spec review** — User reviews the spec file; revise **up to 3 rounds** per phase. If still not approved, **stop** and ask whether to narrow scope, split the phase, or pause PDD.  
   - **Plan:** `superpowers:writing-plans` → plan path under **Canonical paths**.  
   - **Plan approval** — Post Approval Ask ([`reference.md`](reference.md)); wait for explicit approval per **Gate equivalence**.  
   - **Optional todos file** — If progress tracking is **enabled** on the roadmap: after the user approves the phase plan, create or refresh `docs/roadmaps/<feature>/phase-<N>-todos.md` — **sections and tasks mirror the approved plan** (same order and granularity as the plan’s implementation tasks); use checkboxes and brief notes; **update during implementation** (check off, note blockers). If progress tracking is **declined** on the roadmap, do **not** create `phase-*-todos.md`.  
   - **Implement** — TDD / `executing-plans` / `subagent-driven-development` per project norms; keep `phase-<N>-todos.md` in sync if enabled. If scope drifts, follow **Mid-phase scope** in [`reference.md`](reference.md); do not silently expand.  
   - **Verify** — `superpowers:verification-before-completion`, then `superpowers:requesting-code-review`, before claiming done.  
   - **Merge** — `finishing-a-development-branch`; then **immediately** update the roadmap row for this phase (**merged**, PR link if column exists, `Updated`, spec/plan links).  
   - **Advance roadmap row during the phase** — When spec exists, plan exists, user approved plan, PR open — set Status per [`reference.md`](reference.md) transition rules.  
   - **Phase completion signal (recommended)** — After merge, output one line: `PDD phase <N> complete: roadmap=<path> pr=<url-or-none>`  
6. **Next phase** — Only after current phase is **merged** and roadmap reflects it.

On failure while **implementing**: **`superpowers:systematic-debugging`** before guessing fixes.

## Failure, stall, and abort

| Situation | Required action |
|-----------|-------------------|
| `superpowers:brainstorming` or `writing-plans` fails, or expected artifact missing | Retry **once**; if still failing, report the error, **stop** the phase — do not claim the artifact exists |
| User is vague on a gate | Ask one clarifying question: approve as-is **vs** list edits |
| User says pause / stop / abandon phase or PDD | Stop implementation; set roadmap row to **`blocked`** or add a truthful note; do **not** start the next phase |
| Planning-step error (tool/skill failure before code) | Same retry-once rule; escalate to user with what failed |

## Skill interop

| Step | Skill | Output / owner |
|------|--------|----------------|
| Progress preference | **PDD** | Ask before Phase 0; record on roadmap |
| Roadmap | `superpowers:brainstorming` (Phase 0) | `docs/roadmaps/<feature>-roadmap.md` |
| Roadmap gate | **PDD** | User approval (see Gate equivalence) |
| Phase spec | `superpowers:brainstorming` | `docs/superpowers/specs/...-design.md` |
| Phase plan | `superpowers:writing-plans` | `docs/superpowers/plans/...md` |
| Phase todos (optional) | **PDD** | `docs/roadmaps/<feature>/phase-<N>-todos.md` if enabled |
| Implement | TDD / `executing-plans` / `subagent-driven-development` | Code |
| Verify | `superpowers:verification-before-completion`, `superpowers:requesting-code-review` | Evidence |
| Merge | `finishing-a-development-branch` | PR merged |
| Roadmap status | **PDD (required)** | Update roadmap file on transitions + after each merge |
| Debug | `superpowers:systematic-debugging` | — |

## Rules (checklist)

- [ ] **Before Phase 0:** asked whether to use per-phase `phase-<N>-todos.md` progress files; roadmap records **Progress tracking: enabled** or **declined**.
- [ ] Phase 0 delivers **roadmap file only** — not the whole-feature spec, not `writing-plans` for the whole feature.
- [ ] **Roadmap approval** received before Phase 1 spec brainstorming.
- [ ] **Roadmap file** status table updated when phase advances (`spec` → `plan` → `approved` → `in-review` → `merged`) and **after each merge**.
- [ ] **One phase at a time** — next phase only after merge + roadmap updated.
- [ ] **No code** until user approves the **phase plan** (not just “read the plan”).
- [ ] Optional **visuals** only when the user asked.
- [ ] If progress tracking **enabled:** each phase has `phase-<N>-todos.md` aligned to that phase’s **plan**, updated during implementation; if **declined**, no `phase-*-todos.md` files.
- [ ] **Spec review:** at most **3** revision rounds per phase unless user agrees to continue.
- [ ] **Mid-phase scope:** if requirements change during implementation, follow [`reference.md`](reference.md) — no silent expansion.

## What lives in `reference.md`

Read it whenever executing PDD:

- Phase 0 brainstorming **contract** (paste / follow).
- **Roadmap OK** and **plan approval** copy-paste lines, plus **Gate equivalence** (approved phrasing).
- **Status** values and **transition rules**.
- Roadmap document **shape** and **template slot** (you may replace with your full table later).
- Per-phase brainstorming **preamble**.
- Optional **visuals** paths.
- **Mid-phase scope** classification (full table in [`reference.md`](reference.md)).
- **Progress tracking:** verbatim pre-roadmap question, roadmap **Progress tracking** line, and `phase-<N>-todos.md` shape (when enabled).

Per-phase todo files are **optional** and **user-gated** before the roadmap. If the user declines, the roadmap states that and agents **must not** add `phase-*-todos.md`. Otherwise use your usual tracker.
