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
2. **Phase 0 — Roadmap** — `superpowers:brainstorming` with **Phase 0 contract** ([`reference.md`](reference.md)): output **only** `docs/roadmaps/<feature>-roadmap.md`. **Do not** invoke `writing-plans` for the entire feature here. **Do not** write per-phase design specs yet.
3. **Roadmap OK gate** — Present roadmap path + summary. Ask the **roadmap OK** question verbatim ([`reference.md`](reference.md)). **Wait.** No Phase 1 spec work until the user approves (or you revise and re-ask).
4. **For each phase (sequential)**  
   - **Spec:** `superpowers:brainstorming` for **this phase only** → `docs/superpowers/specs/<date>-<topic>-design.md` (use `-phase<N>` in `<topic>` when useful). Optional visuals **only** if the user requested ([`reference.md`](reference.md)).  
   - **Spec review** — Per brainstorming: user reviews the spec file; revise until approved.  
   - **Plan:** `superpowers:writing-plans` → `docs/superpowers/plans/<date>-<feature>.md` (add `-phase<N>` when useful).  
   - **Plan approval** — Post Approval Ask ([`reference.md`](reference.md)); wait for explicit yes.  
   - **Implement** — TDD / `executing-plans` / `subagent-driven-development` per project norms.  
   - **Verify** — `verification-before-completion` before claiming done.  
   - **Merge** — `finishing-a-development-branch`; then **immediately** update the roadmap row for this phase (**merged**, PR link if column exists, `Updated`, spec/plan links).  
   - **Advance roadmap row during the phase** — When spec exists, plan exists, user approved plan, PR open — set Status per [`reference.md`](reference.md) transition rules.
5. **Next phase** — Only after current phase is **merged** and roadmap reflects it.

On failure while implementing: **`systematic-debugging`** before guessing fixes.

## Skill interop

| Step | Skill | Output / owner |
|------|--------|----------------|
| Roadmap | `superpowers:brainstorming` (Phase 0) | `docs/roadmaps/<feature>-roadmap.md` |
| Roadmap gate | **PDD** | User says roadmap OK |
| Phase spec | `superpowers:brainstorming` | `docs/superpowers/specs/...-design.md` |
| Phase plan | `superpowers:writing-plans` | `docs/superpowers/plans/...md` |
| Implement | TDD / `executing-plans` / `subagent-driven-development` | Code |
| Verify | `verification-before-completion` | Evidence |
| Merge | `finishing-a-development-branch` | PR merged |
| Roadmap status | **PDD (required)** | Update roadmap file on transitions + after each merge |
| Debug | `systematic-debugging` | — |

## Rules (checklist)

- [ ] Phase 0 delivers **roadmap file only** — not the whole-feature spec, not `writing-plans` for the whole feature.
- [ ] **Roadmap OK** received before Phase 1 spec brainstorming.
- [ ] **Roadmap file** status table updated when phase advances (`spec` → `plan` → `approved` → `in-review` → `merged`) and **after each merge**.
- [ ] **One phase at a time** — next phase only after merge + roadmap updated.
- [ ] **No code** until user approves the **phase plan** (not just “read the plan”).
- [ ] Optional **visuals** only when the user asked.

## What lives in `reference.md`

Read it whenever executing PDD:

- Phase 0 brainstorming **contract** (paste / follow).
- **Roadmap OK** and **plan approval** copy-paste lines.
- **Status** values and **transition rules**.
- Roadmap document **shape** and **template slot** (you may replace with your full table later).
- Per-phase brainstorming **preamble**.
- Optional **visuals** paths.
- **Mid-phase scope** classification (full table in [`reference.md`](reference.md)).

PDD does **not** require per-phase todo files or session mirroring; track execution however your team prefers (issue tracker, checklist, etc.).
