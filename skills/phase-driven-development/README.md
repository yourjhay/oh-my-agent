# Phase-Driven Development (PDD)

**Roadmap first. One phase at a time. No surprise scope.**

PDD is an agent skill for **large, multi-surface features**: it forces a **shared roadmap** before deep design, then runs **per-phase** brainstorm → spec → plan → **explicit approval** → implement → verify → merge—while a **single roadmap file** stays honest about where each phase really is.

It is **stack-agnostic** and delegates heavy lifting to the [Superpowers](https://claude.com/plugins/superpowers) skills (`brainstorming`, `writing-plans`, TDD, verification, merge completion, and more).

---

## Why use it

| Problem | How PDD helps |
|--------|-----------------|
| Agents jump to a giant plan or code and lose the thread | **Phase 0 is roadmap-only**—no whole-feature `writing-plans` until the roadmap is approved |
| Stakeholders never explicitly agreed to the split | **Roadmap gate** before any Phase 1 spec work |
| Status in docs doesn’t match reality | **G7-style transitions**: update `docs/roadmaps/<feature>-roadmap.md` as spec, plan, PR, and merge happen |
| Checklists appear when nobody asked | **User-gated** optional per-phase `phase-<N>-todos.md` (asked *before* the roadmap) |
| Scope creeps mid-implementation | **Mid-phase scope** rules in `reference.md`—pause, re-spec, re-approve when needed |

---

## Feature highlights

- **Qualification rules** — Clear **when to use** and **when not to** (so the skill doesn’t fire on tiny fixes).
- **Verbatim gates** — Copy-paste **roadmap OK** and **plan approval** prompts, plus **gate equivalence** (e.g. `yes` / `lgtm` / `proceed` where appropriate).
- **Sequential phases** — Next phase only after the current one is **merged** and the roadmap row is updated.
- **Bounded spec review** — Up to **three** revision rounds per phase, then escalate (narrow scope, split phase, or pause).
- **Failure handling** — Retry-once for planning tools; rules for vague users, abort, and blocked states.
- **Optional progress tracking** — Live checklists derived from the **approved** plan, only if the user opted in on the roadmap.

Artifacts live under predictable paths: roadmap at `docs/roadmaps/`, specs/plans under `docs/superpowers/`, optional todos under `docs/roadmaps/<feature>/`. See [`SKILL.md`](SKILL.md) for the normative naming table.

---

## Workflow (end-to-end)

```
Qualify feature → Ask todo-files preference → Phase 0: roadmap ONLY
        → Roadmap approval gate → FOR EACH phase (in order):
              brainstorm (this phase) → spec doc → spec review (≤3 rounds)
              → writing-plans → plan approval gate → [optional todo file]
              → implement → verify → merge → update roadmap row → next phase
```

**Phase 0 output:** one file, `docs/roadmaps/<feature>-roadmap.md`, including a **Progress tracking** line (enabled or declined).

**Each later phase:** design doc + plan doc for **that phase only**, explicit **yes** before code, then implementation aligned with [Superpowers](https://claude.com/plugins/superpowers) workflows your repo already uses.

---

## What’s in this package

| File | Purpose |
|------|---------|
| [`SKILL.md`](SKILL.md) | Main orchestration: triggers, state machine, paths, interop table, checklists |
| [`reference.md`](reference.md) | Phase 0 contract text, gate wording, status vocabulary, G7 transitions, roadmap template slot, mid-phase scope, optional visuals |

Install **both** files into the same folder (e.g. `.claude/skills/phase-driven-development/`). The skill’s YAML **`name`** is **`phase-driven-development`**.

For curl-based install and repo layout, see the main [README](../../README.md) in **oh-my-agent**.
