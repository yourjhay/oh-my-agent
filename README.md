# oh-my-agent

A small collection of reusable AI-agent rules and skills for software projects. Stack-agnostic, drop-in, and works with **Claude Code** and **opencode** out of the box (other agents that read `AGENTS.md` and `SKILL.md` should also work).

## Contents

| Artifact | Type | Purpose |
|----------|------|---------|
| [`phase-driven-development/SKILL.md`](phase-driven-development/SKILL.md) | Claude Code skill | Orchestrates features that qualify for phasing through a brainstorm → spec → plan → approval-gate → implement cycle, with resumable state via a roadmap file and per-phase todos. Delegates each step to [superpowers](https://claude.com/plugins/superpowers) when installed; ships with built-in fallback procedures otherwise. |
| [`SELF-MAINTAIN-DOC-PROMPT.MD`](SELF-MAINTAIN-DOC-PROMPT.MD) | Agent prompt | Bootstraps a self-maintaining docs system in any repo. Paste into your agent at the project root and follow the flow. |
| [`QA_RULES.md`](QA_RULES.md) | Rule file | Strict senior-QA review protocol — code quality, correctness, security, maintainability. Loaded as persistent rules via `CLAUDE.md` import. |

---

## Install

Two paths:

- **A. Prompt install (easiest)** — paste one block into your agent; it detects the harness and installs everything.
- **B. Manual install (curl)** — explicit commands per artifact, full control.

> Source: [`yourjhay/oh-my-agent`](https://github.com/yourjhay/oh-my-agent). Raw URL base used in commands below: `https://raw.githubusercontent.com/yourjhay/oh-my-agent/main`. To pin a specific commit instead of `main`, replace `main` with the commit SHA.

---

### A. Prompt install (any agent — Claude Code, opencode, Cursor, etc.)

Paste the block below into your agent. It works in any chat-style coding agent. The agent detects which harness it's running in, asks whether you want global or per-project install, fetches files from this repo, and wires up imports.

````
Install the `oh-my-agent` toolkit for the agent harness you're running in.

Repo: https://github.com/yourjhay/oh-my-agent
Raw base: https://raw.githubusercontent.com/yourjhay/oh-my-agent/main

Artifacts:
1. SKILL — `phase-driven-development/SKILL.md`
2. RULE FILE — `QA_RULES.md`
3. ONE-SHOT PROMPT — `SELF-MAINTAIN-DOC-PROMPT.MD`

Steps you must perform:

1. **Ask me first**, before doing anything else:
   > "Install `oh-my-agent` GLOBALLY (available in every project on this machine) or PER-PROJECT (just this repo)?"
   Wait for my explicit answer (`global` or `per-project`). Do not proceed until I respond.

2. Detect the harness by checking which dirs/files exist:
   - Claude Code: `~/.claude/` or `CLAUDE.md` in cwd
   - opencode: `~/.config/opencode/` or `AGENTS.md` in cwd, or `opencode.json`
   - Other (Cursor, Aider, etc.): note it, then default to writing to `.claude/` paths since most agents read those as a fallback.

3. Choose paths based on harness + scope:
   - Claude Code global: skills → `~/.claude/skills/`, rules → `~/.claude/rules/`, instruction file → `~/.claude/CLAUDE.md`
   - Claude Code per-project: skills → `.claude/skills/`, rules → `.claude/rules/`, instruction file → `./CLAUDE.md`
   - opencode global: skills → `~/.config/opencode/skills/` (or `~/.claude/skills/`), rules → `~/.config/opencode/`, instruction file → `~/.config/opencode/AGENTS.md`
   - opencode per-project: skills → `.opencode/skills/` (or `.claude/skills/`), rules → project root, instruction file → `./AGENTS.md`

4. For each artifact, fetch from the raw base URL and write to disk:
   a. SKILL → `<skills-dir>/phase-driven-development/SKILL.md` (preserve the directory name)
   b. RULE FILE → `<rules-dir>/qa-rules.md`
   c. ONE-SHOT PROMPT → save as `<scope-root>/SELF-MAINTAIN-DOC-PROMPT.MD` for later reference; do NOT import into the instruction file (it is a one-time bootstrap prompt, not a persistent rule)

5. Wire up the rule file by appending an import line to the instruction file (CLAUDE.md or AGENTS.md):
   - Claude Code: append `@rules/qa-rules.md` (global) or `@.claude/rules/qa-rules.md` (per-project)
   - opencode: append `@qa-rules.md` (path relative to instruction file)
   - If the import line already exists, skip.
   - If the instruction file does not exist, create it with the import line.

6. Verify each file:
   - SKILL.md has YAML frontmatter with `name:` and `description:`
   - qa-rules.md is non-empty
   - Instruction file contains the import line

7. Report a summary table: artifact, destination path, status (installed | skipped | failed).

Constraints:
- Do not commit or push anything.
- Do not modify any files outside the install paths above.
- If a destination file already exists with different content, ask before overwriting.
- If you cannot fetch a URL, report which one and stop.

Optional: also install `superpowers` (companion plugin that the SKILL delegates to) if I'm on Claude Code:
- Run `/plugin install superpowers@claude-plugins-official` and confirm.
- If not on Claude Code or I decline, skip. The skill works without it via built-in fallback procedures.
````

After pasting, answer the agent's "global vs per-project" question and let it run. It should report back with paths.

---

### B. Manual install (curl)

Pick one method per artifact. Skills must be files; one-shot prompts can be pasted.

#### B1. `phase-driven-development` skill (file install)

The skill lives on disk; the agent harness discovers it. Both Claude Code and opencode scan the same `.claude/skills/` paths, so a single install works for both.

**Global (recommended)** — available in every project, both agents:

```bash
mkdir -p ~/.claude/skills/phase-driven-development
curl -fsSL https://raw.githubusercontent.com/yourjhay/oh-my-agent/main/phase-driven-development/SKILL.md \
  -o ~/.claude/skills/phase-driven-development/SKILL.md
```

**Per-project** — checked into the project's `.claude/` dir (both agents read this):

```bash
mkdir -p .claude/skills/phase-driven-development
curl -fsSL https://raw.githubusercontent.com/yourjhay/oh-my-agent/main/phase-driven-development/SKILL.md \
  -o .claude/skills/phase-driven-development/SKILL.md
```

**opencode-native paths** (optional, if you prefer not to use the `.claude/` path):

| Scope | Path |
|-------|------|
| Global | `~/.config/opencode/skills/phase-driven-development/SKILL.md` |
| Per-project | `.opencode/skills/phase-driven-development/SKILL.md` |

**Verify:**
- Claude Code: trigger any feature-planning prompt — `phase-driven-development` should appear in the available-skills list.
- opencode: the `skill` tool will list it; agents can invoke it on-demand.

#### Recommended companion: `superpowers`

The skill delegates to `superpowers:*` skills (brainstorming, writing-plans, test-driven-development, executing-plans, subagent-driven-development, verification-before-completion, systematic-debugging, finishing-a-development-branch). With superpowers installed, each phase step runs the focused skill that owns that practice. Without it, `phase-driven-development` falls back to its own minimum-viable mini-procedures and keeps working — just with less depth.

Install superpowers (Claude Code):

```bash
/plugin install superpowers@claude-plugins-official
```

Source: [superpowers on the Claude plugin marketplace](https://claude.com/plugins/superpowers) · GitHub: [obra/superpowers](https://github.com/obra/superpowers) (by [@obra](https://github.com/obra)).

#### B2. `SELF-MAINTAIN-DOC-PROMPT.MD` (paste prompt)

No file install needed. View raw, copy the contents, paste into your agent at the **root of the target project**:

```bash
# fetch and copy to clipboard (macOS)
curl -fsSL https://raw.githubusercontent.com/yourjhay/oh-my-agent/main/SELF-MAINTAIN-DOC-PROMPT.MD | pbcopy
```

Then paste into your agent and follow its instructions.

#### B3. `QA_RULES.md` (rule file install)

Rule files load every session via an import line in the agent's instruction file (`CLAUDE.md` for Claude Code, `AGENTS.md` for opencode). Install the file, then add the import.

**Claude Code (global)** — applies to every project:

```bash
mkdir -p ~/.claude/rules
curl -fsSL https://raw.githubusercontent.com/yourjhay/oh-my-agent/main/QA_RULES.md -o ~/.claude/rules/qa-rules.md
```

Add to `~/.claude/CLAUDE.md`:

```markdown
@rules/qa-rules.md
```

**Claude Code (per-project)**:

```bash
mkdir -p .claude/rules
curl -fsSL https://raw.githubusercontent.com/yourjhay/oh-my-agent/main/QA_RULES.md -o .claude/rules/qa-rules.md
```

Add to the project's `CLAUDE.md`:

```markdown
@.claude/rules/qa-rules.md
```

**opencode (global)** — applies to every project:

```bash
mkdir -p ~/.config/opencode
curl -fsSL https://raw.githubusercontent.com/yourjhay/oh-my-agent/main/QA_RULES.md -o ~/.config/opencode/qa-rules.md
```

Add to `~/.config/opencode/AGENTS.md` (create if missing):

```markdown
@qa-rules.md
```

**opencode (per-project)**:

```bash
curl -fsSL https://raw.githubusercontent.com/yourjhay/oh-my-agent/main/QA_RULES.md -o qa-rules.md
```

Either reference it from `AGENTS.md` at the project root:

```markdown
@qa-rules.md
```

…or add it to `opencode.json`:

```json
{ "instructions": ["qa-rules.md"] }
```

> opencode also falls back to `~/.claude/CLAUDE.md`, so a Claude Code global install is picked up automatically unless `OPENCODE_DISABLE_CLAUDE_CODE=1` is set.

**Verify:**
- Claude Code: `/memory` should list `qa-rules.md` in loaded context.
- opencode: start a session and confirm the agent references QA rules when asked to review code.

---

## Why these choices

- **Skill → file install.** Both Claude Code and opencode discover skills by scanning `.claude/skills/` (and opencode also checks `.opencode/skills/`). A pasted prompt won't register as an invocable skill. Single-file `curl` beats full `git clone` because the skill is one self-contained file.
- **Rule file → file install + import.** Rules need to load into every session automatically; `CLAUDE.md` (Claude Code) and `AGENTS.md` (opencode) do this via `@...` imports. Pasting would only apply for one turn.
- **One-shot prompt → paste.** The self-maintain doc is a one-time bootstrap prompt the agent reads top-to-bottom. There's nothing for the harness to "install" — pasting at session start is the simplest delivery, and works identically in both agents.

---

## Updating

Re-run the same `curl` command to pull the latest version. To pin to a specific commit, replace `main` in the URL with the commit SHA.

---

## Layout

```
oh-my-agent/
├── README.md
├── LICENSE
├── phase-driven-development/
│   └── SKILL.md
├── SELF-MAINTAIN-DOC-PROMPT.MD
└── QA_RULES.md
```

---

## License

MIT — see [LICENSE](LICENSE).
