# PDD — reference (`phase-driven-development-v2`)

Companion to [`SKILL.md`](SKILL.md). Contracts, gates, status rules, and roadmap template slot.

---

## Phase 0 — brainstorming contract (roadmap only)

When producing the **first** artifact for a PDD feature:

1. Announce: *Using `superpowers:brainstorming` to produce the **roadmap only** (PDD Phase 0).*
2. **Deliverable:** `docs/roadmaps/<feature>-roadmap.md` only — follow the **Roadmap document** section below (use the template slot until your team pastes a full table).
3. **Do not** invoke `writing-plans` for the **entire** feature in this step.
4. **Do not** write `docs/superpowers/specs/...` for individual phases yet — that starts **after roadmap OK**.

After the file exists, run the **Roadmap OK gate** (below).

---

## Roadmap OK gate (before Phase 1 spec)

Present the roadmap path and a short summary (phases, order, dependencies). Then ask **exactly**:

> Does this roadmap look good to proceed to Phase 1 spec work? Reply **roadmap OK** to proceed, or describe **changes** you want.

**Wait.** Do not start Phase 1 `superpowers:brainstorming` for a phase spec until the user responds with roadmap OK (possibly after edits).

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

---

## Roadmap document — path and template slot

**Path:** `docs/roadmaps/<feature>-roadmap.md`

**Author note:** Replace the **Template slot** subsection below with your team’s full roadmap table when ready. Extend the minimal example with any columns (e.g. **Todos**, links) your process needs.

### Template slot (minimal example — replace with your canonical table)

Use this shape until you paste the full template:

```markdown
# <Feature> Roadmap

**Goal:** <one sentence>
**Owner:** <team>  **Started:** <YYYY-MM-DD>

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

Adjust columns to match your process (e.g. add links columns you need). **Todos** column is optional — PDD does not mandate per-phase todo files.

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
