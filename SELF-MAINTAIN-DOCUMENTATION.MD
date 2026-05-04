# Simple Universal Docs System Setup Prompt (Docs-First + Fast Context Map)

Use this prompt in an AI coding agent (Cursor/Claude/etc.) at the **root of a target project**.

---

## Instructions for the Agent

You are setting up a self-maintaining documentation system for THIS repository.

### Main goal

Make future sessions fast and consistent by ensuring the agent can orient to any module in seconds using docs-first flow:

1. Read `docs/context-map.json` (if present) or `docs/features/_index.md` first
2. Read the module doc in `docs/features/`
3. Search source code only for missing details (use relevant skills & tools if available)
4. Make sure existing docs is still respected. Don't overwrite it.

---

## Hard rules (must follow) - MAKE SURE THIS IS IMPLEMENTED

- NEVER commit & push changes unless the user explicitly asks you to.
- Do not begin implementation until design is approved.
- Keep changes minimal, incremental, and reviewable.
- Prefer existing project structure and naming conventions. (or if project has built in file generation use it like laravel)
- Reuse existing scripts/patterns where possible.
- If uncertain about stack, structure, or module boundaries, ask before proceeding.

---

## Phase 1 — Discovery (report only, then stop)

If you are not sure about the project structure, stack, or module boundaries:

- Ask the user clarifying questions first.
- Do not assume or guess.
- If module boundaries remain unclear, propose 2-3 boundary options and ask the user to choose.

Inspect repo and report:

1. Stack and runtime(s): PHP/Node/Python/etc.
2. Current docs structure (if any)
3. Likely module boundaries
4. Existing check/test command(s)
5. Existing AI/conventions files (`CLAUDE.md`, `AGENTS.md`, `.cursor/rules`, etc.)

Also ask the user (cheap to answer now, avoids rework in Phase 2):

- **Context-map preference**: "Do you want a `docs/context-map.json` (machine-readable index with build/check scripts), or is `docs/features/_index.md` enough?" Default to `_index.md` only for small repos (<5 modules).

Then report back clearly with the discovered details + the user's context-map choice, and stop to wait for approval.

---

## Phase 2 — Design (write plan, then stop)

Propose exact design for this repo with:

### A) Required docs structure

- `docs/AI_DEVELOPMENT_RULES.md`
- `docs/README.md`
- `docs/features/_index.md`
- `docs/features/<module>.md` for each module

Optional (only if user opted in during Phase 1 discovery):

- `docs/context-map.json` — machine-readable module index for fast agent lookup
- `docs/context-map.schema.json` — JSON Schema for validation

If `context-map.json` was opted in, also propose:

- **Canonical runtime for docs tooling** (e.g., PHP-in-Docker, Node, Python) — pick one matching the project stack. Optionally a secondary variant (e.g., `.mjs` alongside PHP) if useful.

### B) Docs-first workflow policy

- Agent must read `docs/context-map.json` (if present) or `docs/features/_index.md` before broad code search.
- Agent must read relevant module doc before source inspection.
- Broad grep/search is disallowed unless docs are insufficient.

### C) Documentation coverage policy

- Every created/updated feature must be documented thoroughly.
- For bug fixes/refactors: update docs if behavior/contracts/responsibilities/data flow/integrations changed.
- Purely internal refactors with zero behavior/interface impact may skip the feature doc update — but `_index.md` (and `context-map.json` if used) must still reflect any added/removed/renamed modules.

### D) Completion gate

- Work is not complete until required docs are updated.
- If feature docs changed, `_index.md` must be updated, and `context-map.json` regenerated (if used).

### E) Validation scripts (only if `context-map.json` was opted in)

Skip this section entirely if the user declined `context-map.json`.

Create two scripts:

1. **build context map**
   - Read `docs/features/_index.md`
   - Parse feature docs
   - Generate `docs/context-map.json`

2. **check context map**
   - Validate required shape/fields
   - Ensure every indexed module exists in map
   - Ensure doc paths exist
   - Optional stale-doc check using git changed files:
     - If module source changed, require module doc or `context-map.json` update

Provide canonical + optional runtime variants:
- Canonical: stack-default runtime
- Optional: secondary runtime variant if useful (e.g., `.mjs`)

Then stop and wait for approval.

---

## Phase 3 — Implement foundation (after approval)

Implement:

1. `docs/README.md` with docs-first instructions
2. `docs/AI_DEVELOPMENT_RULES.md` containing the Hard rules + Docs-first workflow policy + Documentation coverage policy + Completion gate from this prompt
3. `docs/features/_index.md` with current module list
4. `docs/features/<module>.md` for each identified module
5. Update existing AI rules/conventions files (`CLAUDE.md` / `AGENTS.md` / `.cursor/rules`) to point at `docs/AI_DEVELOPMENT_RULES.md` and enforce docs-first flow

If `context-map.json` was opted in, also implement:

6. `docs/context-map.schema.json`
7. Context-map scripts (`build` + `check`)
8. Initial generated `docs/context-map.json`
9. Commands wired into project scripts:
   - canonical runtime commands
   - optional runtime commands if applicable

---

## Feature doc template (minimum) - Important

Each `docs/features/<module>.md` must include:

- Feature name
- Description
- Inputs / outputs
- Usage examples
- Related modules/files
- Entry points (routes/controllers/components/jobs/commands/events)
- Data touchpoints (models/tables/cache/queues/integrations)
- Key behavior, edge cases, limitations
- Permissions/roles + side effects (if applicable)

---

## Required checks before finishing

Run and report:

1. All modules in `_index.md` have a corresponding `docs/features/<module>.md`
2. If `context-map.json` opted in: build + check commands pass
3. No lint/format issues introduced in changed files
4. Summarize changed files and next maintenance steps

---

## Final deliverable format
- Verify Hard rules if implemented. IMPORTANT
- Audit/Check the current implementation and provide enhancements or improvements (Present the changes or plan and ask for approval)

At completion, provide:
- Short summary of what was created
- Exact commands to regenerate/check docs
- Maintenance checklist for future contributors
