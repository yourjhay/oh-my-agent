# PDD — reference (`phase-driven-development`)

Companion to [`SKILL.md`](SKILL.md). Contracts, gates, status rules, and roadmap template slot.

---

## Progress tracking preference (before Phase 0 roadmap)

**Before** you run Phase 0 (`superpowers:brainstorming` for the roadmap), **ask** the user **exactly**:

> Do you want a **real-time record** of implementation progress: per-phase checklist files `docs/roadmaps/<feature>/phase-<N>-todos.md`, structured from each phase’s **approved plan** and updated as work proceeds? Reply **yes** or **no**.  
> If **no**, the roadmap will record that choice so we do **not** create `phase-*-todos.md` files.

**Wait** for an answer, then include a **Progress tracking** line in the roadmap (see **Roadmap document** below): either **enabled** (and optional link column to each phase’s todo file) or **declined** with a short note that per-phase todo files must not be generated.

---

## Phase 0 — brainstorming contract (roadmap only)

When producing the **first** artifact for a PDD feature:

1. **Already asked** progress-tracking preference (section above); roadmap will include the **Progress tracking** line.
2. Announce: *Using `superpowers:brainstorming` to produce the **roadmap only** (PDD Phase 0).*
3. **Deliverable:** `docs/roadmaps/<feature>-roadmap.md` only — follow the **Roadmap document** section below (use the template slot until your team pastes a full table).
4. **Do not** invoke `writing-plans` for the **entire** feature in this step.
5. **Do not** write `docs/superpowers/specs/...` for individual phases yet — that starts **after roadmap OK**.

After the file exists, run the **Roadmap OK gate** (below).

---

## Roadmap OK gate (before Phase 1 spec)

Present the roadmap path and a short summary (phases, order, dependencies). Then ask **exactly**:

> Does this roadmap look good to proceed to Phase 1 spec work? Reply **roadmap OK** to proceed, or describe **changes** you want.

**Wait.** Do not start Phase 1 `superpowers:brainstorming` for a phase spec until the user responds with roadmap OK (possibly after edits).

**Gate equivalence (counts as roadmap approval):** explicit phrase **roadmap OK**, or clear approval such as **yes**, **approved**, **lgtm**, **proceed**, **looks good**, **ship it** (after any requested edits are applied). If ambiguous, ask one binary follow-up: proceed as-is vs list changes.

---

## Status vocabulary

| Status | Meaning |
|--------|---------|
| `planned` | In roadmap; work not started |
| `brainstorming` | Design exploration / drafting spec (optional for roadmap row if you track Phase 0) |
| `spec` | Phase spec doc exists (path in Spec column if present) |
| `plan` | Phase plan doc exists |
| `approved` | User approved implementation for this phase |
| `in-review` | PR open |
| `merged` | Landed on main — phase done |
| `blocked` | Waiting on external factor (note why) |

---

## Transition rules (G7)

For the **active** phase row in `docs/roadmaps/<feature>-roadmap.md`:

1. When the phase **spec** file is written → set Status to **`spec`** (and Spec link if column exists).
2. When **`writing-plans`** finishes for that phase → **`plan`** (Plan link).
3. When the user **approves** implementation after the plan approval ask → **`approved`**.
4. When a **PR** is opened → **`in-review`** (PR link).
5. When the PR **merges** → **`merged`**, set **Updated** date, ensure Spec / Plan / PR columns are filled if your table has them.

**Minimum:** after **every** phase merge, the roadmap **must** reflect **`merged`** for that phase and current **Updated** date. Prefer updating at each step above so the table never lies.

---

## Plan approval ask (after `writing-plans` for the phase)

Post in chat:

```text
Phase <N> plan ready: <link to docs/superpowers/plans/...>

Scope:
- <one line per major task>

Risks: <one line or none>
Out of scope: <one line>

Ready to proceed with Phase <N> implementation? (yes / changes / no)
```

**Wait** for explicit **yes** (or equivalent) before writing implementation code for that phase.

**Gate equivalence (counts as plan approval):** **yes**, **approved**, **lgtm**, **proceed**, **ship it**, or equivalent explicit consent. Do **not** treat “I read it” as approval. If ambiguous, ask one binary follow-up.

---

## Roadmap document — path and template slot

**Path:** `docs/roadmaps/<feature>-roadmap.md`

**Author note:** Replace the **Template slot** subsection below with your team’s full roadmap table when ready. Extend the minimal example with any columns (e.g. **Todos**, links) your process needs.

**Progress tracking (required line in every PDD roadmap):** record the user’s choice from [Progress tracking preference](#progress-tracking-preference-before-phase-0-roadmap).

- **Enabled example:** `**Progress tracking:** enabled — per-phase files at docs/roadmaps/<feature>/phase-<N>-todos.md`
- **Declined example:** `**Progress tracking:** declined — do not create phase-*-todos.md files`

### Template slot (minimal example — replace with your canonical table)

Use this shape until you paste the full template:

```markdown
# <Feature> Roadmap

**Goal:** <one sentence>
**Owner:** <team>  **Started:** <YYYY-MM-DD>
**Progress tracking:** <enabled — path pattern above | declined — no phase-*-todos.md>

## Phases

| Phase | Title | Status | Spec | Plan | Visual | PR | Updated |
|-------|-------|--------|------|------|--------|----|---------|
| 1 | <title> | planned | — | — | — | — | <YYYY-MM-DD> |

## Phase Notes

### Roadmap approved

Roadmap approved <YYYY-MM-DD> (after user says roadmap OK.)

### Phase 1 — <title>
<short scope / why first>

## Out of scope

- <item>
```

Adjust columns to match your process (e.g. add a **Todos** link column when progress tracking is **enabled**). When **declined**, omit that column or leave it **—**.

---

## Per-phase `phase-<N>-todos.md` (when progress tracking is enabled)

**Path:** `docs/roadmaps/<feature>/phase-<N>-todos.md` (same `<feature>` as the roadmap file name prefix).

**When:** Create or refresh **after** the user approves the phase plan (not before), so the file matches the **approved** plan.

**Format:** Mirror the **phase plan** document for that phase:

- Same **section headings** and **task order** as the plan’s implementation steps (or a one-to-one checklist derived from them).
- Use `- [ ]` / `- [x]` for tasks; optional short sub-bullets for notes or blockers.
- Top of file: link to the phase **plan** and **spec**; **Updated** date when you make meaningful progress.

**While implementing:** Check items off, add brief notes, and keep the file consistent with the plan. If the plan is revised per [Mid-phase scope changes](#mid-phase-scope-changes), update this file to match.

**When progress tracking is declined:** Do **not** create these files.

---

## Per-phase brainstorming (after roadmap OK)

For **each** phase, in order:

1. Announce: *Using `superpowers:brainstorming` for **Phase <N> spec**.*
2. Produce `docs/superpowers/specs/<date>-<topic>-design.md` — append **`-phase<N>`** to `<topic>` when it avoids ambiguity.
3. **Visuals:** Only if the user **requested** visual artifacts for this work. If yes, record paths (see below) and optional Visual column in roadmap.

Follow normal brainstorming: write spec, commit if applicable, **user reviews spec file**, then invoke **`writing-plans`** for that phase only.

---

## Optional visuals

Only when the user asks. Typical pattern:

`docs/roadmaps/<feature>/phase<N>-visual.<ext>`

List in roadmap Visual column or Phase Notes if your template includes them.

---

## Mid-phase scope changes

If scope grows or drifts during implementation, **stop**. Do not silently expand.

| Type | Action |
|------|--------|
| Bug in plan (wrong path, missing dependency, broken test) | Fix the plan and implementation; note the deviation in the PR or team log. |
| New work **within** the phase (≤2 added tasks, same goal, acceptance criteria unchanged) | Append to the plan; note the deviation; get user re-approval if your bar requires it. |
| **New scope** (changes the goal, breaks acceptance criteria, or >2 added tasks) | **Pause.** Update the spec for this phase, refresh the plan, and run the **plan approval gate** again before more implementation. |
| Separate concern (different goal or belongs in another phase) | Add or defer to another phase in the roadmap; do not bundle unrelated work into the current phase. |

### Re-approve flow (when “new scope” triggers)

1. Pause implementation; commit or stash work safely.
2. Update the phase **spec** and **plan** to match reality.
3. Post an **Approval Ask** again (same pattern as first-time plan approval).
4. Resume only after explicit user confirmation.
