# Design: PDD (Phase-Driven Development) skill v2

**Date:** 2026-05-04  
**Status:** Approved for implementation (2026-05-04)  
**Scope:** New orchestration skill (“PDD”) for multi-phase work, integrated with superpowers, token-efficient layout, explicit roadmap approval gate.

**Implementation note — roadmap table template:** The full markdown table for `docs/roadmaps/<feature>-roadmap.md` is **deferred**; the author will add it to `reference.md` later. The shipped `reference.md` will still include **status vocabulary**, **transition rules**, **Phase 0 / per-phase contracts**, and a **minimal placeholder** (or one example row) so agents know where the canonical table belongs — not an empty “TBD” section.

---

## Problem

The existing `phase-driven-development` skill is long to load and overlaps prose with superpowers. Users need a **named workflow (PDD)** that:

1. Starts from **brainstorming** to produce a **phased roadmap** (not a monolithic spec).
2. Requires an explicit **“roadmap OK?”** checkpoint before any Phase 1 spec work.
3. Reuses **`superpowers:brainstorming`** per phase for **spec** (and **optional visuals** only when the user requests them).
4. Delegates **plans** to **`superpowers:writing-plans`** per phase after each phase’s spec is written and reviewed per brainstorming rules.
5. Keeps the **main skill file small**; templates and verbatim prompts live in **reference** material.

---

## Goals

- **G1 — Orchestration:** PDD defines order of operations, artifact paths, and gates; superpowers skills do the heavy lifting.
- **G2 — Roadmap first:** First brainstorming pass yields **`docs/roadmaps/<feature>-roadmap.md`** using the roadmap structure and columns defined in **`reference.md`** (full table template may be completed by the repo owner when ready; see **Implementation note** above).
- **G3 — Roadmap gate:** No Phase 1 brainstorming-for-spec until the user explicitly approves the roadmap (“roadmap OK?” / equivalent).
- **G4 — Per-phase cycle:** For each phase: brainstorming → spec → user reviews spec (per brainstorming) → writing-plans → approval gate → implement → merge; **one phase at a time** until merged.
- **G5 — Token efficiency:** `SKILL.md` ≤ ~120–150 lines of high-signal content; long templates in `reference.md` (or `references/*.md`).
- **G6 — Visuals:** Optional; only if the user requests; paths documented in reference (e.g. optional Visual column, `phase<N>-visual.<ext>`).
- **G7 — Roadmap status:** PDD **always** updates **`docs/roadmaps/<feature>-roadmap.md`** so phase rows reflect reality — **at minimum** after **each phase merges** (status, links if present, `Updated`); **reference.md** defines status vocabulary and when to transition rows during spec/plan/PR work so the table stays current.

## Non-goals

- Replacing superpowers brainstorming or writing-plans content rules.
- Defining stack-specific implementation details.
- Auto-generating code or skipping user approval on plans.
- **Extra bookkeeping:** PDD does **not** mandate per-phase **todo files**, session-task mirroring, or “freeze todos” rituals — use **v1** `phase-driven-development` if the team wants that model.

---

## Users and primary flow

**Actor:** Developer or agent with user in the loop.

**End-to-end flow:**

1. **Qualify:** Feature/module(s) meet phasing criteria (same family as v1: e.g. 2+ persistence surfaces, 2+ user-facing surfaces, background work, multi-domain, natural rollout). If unsure, ask whether to use PDD.
2. **Invoke PDD.**
3. **Phase 0 — Roadmap (brainstorming, scoped):**  
   - Announce: *Using `superpowers:brainstorming` to produce the **roadmap only** (PDD Phase 0).*  
   - **Override:** Do **not** treat this as the full “design → spec → then writing-plans for entire feature” loop. Deliverable is **only** the roadmap file from the template.  
   - Output: `docs/roadmaps/<feature>-roadmap.md` (create/update).
4. **Gate — Roadmap OK:**  
   - Present the roadmap path and a short summary (phases, ordering, dependencies).  
   - **Ask explicitly:** e.g. *“Does this roadmap look good to proceed to Phase 1 spec work? (roadmap OK / changes)”*  
   - **Wait.** No Phase 1 spec brainstorming until the user answers **roadmap OK** (or confirms after edits).  
   - If the user requests changes: update the roadmap (may re-involve brainstorming with the same Phase 0 contract); re-ask until approved.
5. **For each phase (sequential):**  
   - **Brainstorming (full phase spec):** Produce `docs/superpowers/specs/<date>-<topic>-design.md` for **that phase** (append `-phase<N>` to `<topic>` when needed). Optional visuals only if the user asked — artifacts and naming in reference.  
   - **Spec review:** Per brainstorming: user reviews written spec; revise until approved.  
   - **Plan:** Invoke **`superpowers:writing-plans`** → `docs/superpowers/plans/<date>-<feature>.md` (with `-phase<N>` when needed).  
   - **Plan approval gate:** PDD Approval Ask (same intent as v1); wait for explicit go-ahead.  
   - **Implement:** TDD / subagent or executing-plans per project norms; verification-before-completion before claiming done.  
   - **Finish phase:** Merge to main; **immediately update** the roadmap file: that phase’s row (status → merged, PR link if column exists, `Updated` date, spec/plan links if missing). **Do not** start the next phase until the merge is done **and** the roadmap reflects it.  
   - **During the phase (before merge):** Keep the roadmap row for the active phase aligned with progress (e.g. `spec` → `plan` → `approved` → `in-review`) per **`reference.md`** transition rules — so “after each phase” includes both **lifecycle updates** and the **mandatory post-merge** update.

---

## Skill interop (concise)

| Step | Superpowers / owner | Artifact |
|------|---------------------|----------|
| Roadmap | `superpowers:brainstorming` (Phase 0 contract) | `docs/roadmaps/<feature>-roadmap.md` |
| Roadmap gate | **PDD** | User confirmation in chat |
| Phase spec | `superpowers:brainstorming` | `docs/superpowers/specs/...-design.md` |
| Phase plan | `superpowers:writing-plans` | `docs/superpowers/plans/...md` |
| Implement | TDD / executing-plans / subagent-driven-development | code + commits |
| Verify | `superpowers:verification-before-completion` | evidence |
| Merge / cleanup | `superpowers:finishing-a-development-branch` | PR, merge |
| Roadmap status | **PDD** (required) | `docs/roadmaps/<feature>-roadmap.md` updated each phase / at transitions |
| Debug on failure | `superpowers:systematic-debugging` | — |

**Phase 0 brainstorming contract (must appear in `reference.md`):** One short block the agent pastes or follows so brainstorming output is **roadmap-shaped only** and does **not** invoke writing-plans for the whole feature.

---

## Artifact layout (repo conventions)

- **Roadmap:** `docs/roadmaps/<feature>-roadmap.md` (Phase 0 deliverable; template in `reference.md`). The template **includes** a status table; **PDD requires** keeping it **current** (see **G7**): transitions during work + **mandatory** refresh **after each phase merges**.
- **Specs / plans:** superpowers paths under `docs/superpowers/specs/` and `docs/superpowers/plans/`.

**Roadmap approval** in chat may be echoed in **Phase Notes** as `Roadmap approved YYYY-MM-DD` if the template provides that section. Optional **Phase 0** row “Roadmap” in the table is documented in `reference.md`. First implementation phase for specs remains **Phase 1** unless the template uses only substantive phases.

---

## Token strategy

- **`SKILL.md`:** Frontmatter + when-to-use + state machine + interop table + paths + “read `reference.md` for templates and Phase 0 contract.”
- **`reference.md`:** Roadmap document contract + **status transition rules**; slot or minimal example for the **roadmap table** (author may paste the full template later), Approval Ask variants (including **roadmap OK**), optional Visual column rules, mid-phase scope change summary (optional pointer to v1), Phase 0 / per-phase brainstorming preambles.

---

## Risks and mitigations

| Risk | Mitigation |
|------|------------|
| Agent runs writing-plans after roadmap brainstorming | Phase 0 contract explicit in SKILL + reference; “do not invoke writing-plans until Phase 1 spec exists” for the feature |
| User skips roadmap gate | PDD lists gate as **blocking**; checklist in SKILL |
| Duplication with v1 | v2 can supersede or coexist; README note “use PDD (v2) for roadmap-first flow” |
| Stale roadmap table | G7 + transition rules in `reference.md`; checklist in `SKILL.md` |

---

## Acceptance criteria

- [ ] Design doc (this file) committed under `docs/superpowers/specs/`.
- [ ] Implementation adds `pdd/SKILL.md` (or agreed directory name) + `reference.md`, implements Phase 0 contract, roadmap OK gate, per-phase delegation, and **mandatory roadmap status updates** (G7).
- [ ] Main `SKILL.md` stays short; templates not duplicated in full in the main file.
- [ ] Explicit **roadmap OK** question appears in reference as copy-paste text.

---

## Self-review (2026-05-04)

- **Placeholders:** None intended.
- **Consistency:** Phase 0 avoids full-feature writing-plans; per-phase restores normal superpowers flow.
- **Scope:** Single skill + reference; roadmap status is **owned** by PDD; optional todo-file rituals remain out of scope (non-goals).
- **Ambiguity:** Roadmap approval is explicit in chat; optional dated line in Phase Notes if the template includes it.
- **Implementation (2026-05-04):** `pdd/SKILL.md`, `pdd/reference.md` added; minimal roadmap table in `reference.md` (author may replace with full template). Plan: `docs/superpowers/plans/2026-05-04-pdd-skill.md`.

---

## Next step

After user approves this document, use **`superpowers:writing-plans`** to produce the implementation plan for creating `pdd/SKILL.md` and `reference.md` (and optional README pointer in `agents-rules`).
