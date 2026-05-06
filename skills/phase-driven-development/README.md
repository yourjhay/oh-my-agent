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

## Advantages of using this skill

- **Shared picture early** — You agree on phases and order *before* deep specs, so rework from “wrong shape” discoveries drops.
- **Explicit human checkpoints** — Roadmap gate and plan approval reduce silent assumptions; the agent has to align with you at predictable moments.
- **Auditable trail** — Roadmap + per-phase specs/plans (and optional todo files) make status and history legible for you, reviewers, and future agents.
- **Honest progress** — Synchronous roadmap (G7) ties written artifacts to table state, so “where are we really?” is answerable from one file.
- **Right-sized activation** — When / when-not rules steer PDD at multi-surface work and away from one-line fixes, keeping sessions focused.
- **Composable with Superpowers** — You keep using familiar skills (`brainstorming`, `writing-plans`, TDD, verification, merge) instead of a second, parallel methodology.
- **Stack-agnostic** — Same workflow for web, backend, data, or mixed projects; only paths and repo conventions change.

---

## Feature highlights

- **Qualification rules** — Clear **when to use** and **when not to** (so the skill doesn’t fire on tiny fixes).
- **Verbatim gates** — Copy-paste **roadmap OK** and **plan approval** prompts, plus **gate equivalence** (e.g. `yes` / `lgtm` / `proceed` where appropriate).
- **Sequential phases** — Next phase only after the current one is **merged** and the roadmap row is updated.
- **Bounded spec review** — Up to **three** revision rounds per phase, then escalate (narrow scope, split phase, or pause).
- **Synchronous roadmap (G7)** — After each spec, plan, approval, PR, and merge, the roadmap row is updated **in the same turn**—not fixed retroactively when someone notices.
- **Failure handling** — Retry-once for planning tools; rules for vague users, abort, blocked states, and **stale roadmap** (must correct before continuing).
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

## Sample usage

**Scenario:** Usage-based billing—new persistence, APIs, and UI (multiple surfaces; good fit for PDD).

**You might say:**  
“We’re building usage-based billing: Stripe sync, invoice tables, API, then dashboard. Use PDD.”

**What the agent does (compressed):**

1. Confirms the work qualifies for PDD (several criteria apply).
2. Asks whether you want per-phase checklist files (`phase-<N>-todos.md`); you answer **yes** or **no**—that choice is recorded on the roadmap.
3. **Phase 0 only:** writes `docs/roadmaps/usage-billing-roadmap.md` (phases might be e.g. schema + sync → API → UI). No whole-feature plan yet.
4. **Roadmap gate:** shares path + short summary; you reply **roadmap OK**, **yes**, **lgtm**, or equivalent—then Phase 1 spec work may start.
5. **Phase 1:** brainstorm → `docs/superpowers/specs/<date>-usage-billing-phase1-design.md` → you review the spec → `writing-plans` → **plan approval** → if todos were enabled, `docs/roadmaps/usage-billing/phase-1-todos.md` mirrors the approved plan → implement → verify → merge → roadmap row marked **merged**.
6. **Later phases:** repeat the same loop. The next phase’s spec does **not** start until the prior phase is merged and the roadmap reflects it.

**Optional completion line** (recommended in [`SKILL.md`](SKILL.md)):

```text
PDD phase 1 complete: roadmap=docs/roadmaps/usage-billing-roadmap.md pr=https://github.com/org/repo/pull/123
```

---

## Context footprint (reference)

Rough sizes for estimating how much may land in model context when these files are loaded verbatim. Many harnesses inject [`SKILL.md`](SKILL.md) first and pull in [`reference.md`](reference.md) only when instructed.

### Rough size

| File | Bytes | Ballpark tokens* |
|------|-------:|-----------------:|
| [`SKILL.md`](SKILL.md) | ~12k | ~3.0k |
| [`reference.md`](reference.md) | ~9k | ~2.3k |
| **Total** | **~21k** | **~5–5.5k** |

\*Very rough (≈4 characters per token for English prose). Real tokenization varies by model and tokenizer. Byte counts move as files edit—re-measure locally with:

`wc -c skills/phase-driven-development/SKILL.md skills/phase-driven-development/reference.md`

---

## What’s in this package

| File | Purpose |
|------|---------|
| [`SKILL.md`](SKILL.md) | Main orchestration: triggers, state machine, paths, interop table, checklists |
| [`reference.md`](reference.md) | Phase 0 contract text, gate wording, status vocabulary, G7 transitions, roadmap template slot, mid-phase scope, optional visuals |

Install **both** files into the same folder (e.g. `.claude/skills/phase-driven-development/`). The skill’s YAML **`name`** is **`phase-driven-development`**.

For curl-based install and repo layout, see the main [README](../../README.md) in **oh-my-agent**.
