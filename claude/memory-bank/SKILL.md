---
name: memory-bank
description: Maintain layered project memory. Use when updating memory, organizing memory files, archiving history, marking stale knowledge, recovering context, or converting append-only notes into structured layers.
argument-hint: "[init|update|archive|status] [target-file]"
allowed-tools: Read Write Edit Glob Grep Bash(ls) Bash(mkdir)
---

# Memory Bank Maintainer

Maintain project memory so future agents read less, read the right files, and do not treat stale notes as current truth.

## Arguments

The user invoked `/memory-bank $ARGUMENTS`. Parse the first word as action:

| Action | Meaning |
| --- | --- |
| `status` (default) | Show current memory state: which files exist, last updated, entry counts |
| `init` | Convert existing `memory.md` into layered structure |
| `update <target>` | Update a specific file (current, knowledge, pitfalls, etc.) |
| `archive [YYYY-MM]` | Move old content from current.md to history/YYYY-MM.md |

If no action is given, default to `status`.

## Dynamic Context

Before acting, gather current memory state:

```bash
ls -la memory-bank/ 2>/dev/null || echo "No memory-bank directory"
ls -la memory.md 2>/dev/null || echo "No memory.md"
```

Use this to decide what exists and what needs creating.

## Actions

### status

Display a summary of the memory bank:

- Which files exist (memory-bank/ or memory.md)
- Last modified dates
- Entry counts per file
- Suggested next action

### init

Convert existing `memory.md` into layered structure:

1. Read `memory.md` (or `memory-bank/current.md` if it already exists).
2. Classify content:
   - **Current state** (active phase, latest status, next action) → `memory-bank/current.md`
   - **Stable knowledge** (durable rules, verified commands, architecture) → `memory-bank/knowledge.md`
   - **History** (dated logs, old milestones) → `memory-bank/history/YYYY-MM.md`
   - **Pitfalls** (repeated mistakes, known failures) → `memory-bank/pitfalls.md`
3. Create `memory-bank/` directory if needed.
4. Write classified content to target files using templates from `templates.md`.
5. Keep original `memory.md` as backup (do not delete).
6. Report what was moved where.

### update \<target\>

Update a specific memory file. Targets:

| Target | File |
| --- | --- |
| `current` | `memory-bank/current.md` |
| `knowledge` | `memory-bank/knowledge.md` |
| `pitfalls` | `memory-bank/pitfalls.md` |
| `decisions` | `memory-bank/decisions.md` |
| `commands` | `memory-bank/commands.md` |
| `contracts` | `memory-bank/contracts.md` |

Workflow:
1. Read the target file (create if missing, using template from `templates.md`).
2. Apply the update: prefer **replacing** current facts over appending.
3. Move old facts to history or mark them stale.
4. Preserve unrelated existing content.
5. Report: files changed, facts replaced, items archived.

### archive [YYYY-MM]

Move outdated content from `current.md` to history:

1. Read `memory-bank/current.md`.
2. Identify content that is no longer current (completed work, old status).
3. Create or append to `memory-bank/history/YYYY-MM.md` (default: current month).
4. Remove archived items from `current.md`.
5. Report what was archived.

## Workflow (General)

1. Locate the memory entrypoint before editing:
   - Prefer `memory-bank/current.md`, then `memory/current.md`, then `memory.md`.
   - Also inspect `AGENTS.md` or project instructions when they mention memory rules.
2. Classify the requested update:
   - Current state: active project phase, latest status, immediate constraints, next action.
   - Stable knowledge: durable rules, verified commands, architecture notes, workflow preferences.
   - History: dated progress logs, old milestones, completed sync records, prior experiments.
   - Pitfall: repeated mistake, known failure mode, misleading command, recovery note.
   - Decision: important choice, reason, tradeoff, and current status.
3. Read only the files needed for that class of update.
4. Prefer replacing current facts over appending. Move old facts to history or mark them stale.
5. Preserve user-authored unrelated content. If existing content is ambiguous, ask before rewriting it.
6. End with a concise report of files changed, facts replaced, items archived, and stale notes marked.

## Read Decision Table

Use the smallest sufficient read set.

| User intent | Read first | Read only if needed |
| --- | --- | --- |
| Current progress, resume context, "what is the state?" | `current.md` or `memory.md` | `history/YYYY-MM.md` |
| Update project memory after work | current entrypoint | relevant stable files, current month history |
| Organize or split a memory bank | existing memory file | `AGENTS.md`, nearby history files |
| Tooling, commands, setup, deployment | current entrypoint | `commands.md`, `pitfalls.md` |
| API, data shape, contracts | current entrypoint | `contracts.md`, `decisions.md` |
| Error, regression, repeated mistake | current entrypoint | `pitfalls.md`, relevant history |
| Why a choice was made | current entrypoint | `decisions.md`, relevant history |
| Retrospective or audit | current entrypoint | history files in the requested date range |

Do not read the full memory bank by default. History is an archive, not the working context.

## Write Rules

Use the minimum structure that fits the project:

- Small project: `current.md`, `knowledge.md`, `history/YYYY-MM.md`.
- Growing project: add `pitfalls.md`, `decisions.md`, `commands.md`, or `contracts.md` only when those topics are recurring.
- Do not over-split a memory bank just because templates exist.

Write destinations:

- `current.md`: short active truth, current constraints, latest status, next action. Keep it brief.
- `knowledge.md`: stable rules, verified preferences, durable project facts.
- `history/YYYY-MM.md`: dated progress logs and old states that should not load by default.
- `pitfalls.md`: reusable lessons from repeated failures or misleading assumptions.
- `decisions.md`: decisions with rationale, status, and date.
- `commands.md`: verified commands, expected working directory, and caveats.
- `contracts.md`: APIs, schemas, data contracts, integration assumptions.

## Entry Format

Prefer compact entries with explicit freshness:

```markdown
## Title

Status: active | uncertain | deprecated | historical
Updated: YYYY-MM-DD
Confidence: high | medium | low
Scope: project/module/workflow

Fact or rule in one short paragraph.
```

Use `active` only for current truth. Use `uncertain` for unverified claims. Use `deprecated` for old guidance that may explain history but should not be followed. Use `historical` for archive-only context.

## Write Admission

Write memory only when it is likely to help future work.

Allowed:

- User-confirmed long-term preference.
- Verified command, setup step, or recovery procedure.
- Current project state needed for context recovery.
- Repeated pitfall or recurring mistake.
- Durable decision, tradeoff, or rule.
- Historical milestone or completed sync record.

Avoid:

- One-off chat details.
- Raw temporary logs.
- Unverified AI guesses presented as fact.
- Old plans without status.
- Content already represented clearly elsewhere.
- Large copied documents when a short summary and source path are enough.

## Safety Rules

Never store raw secrets:

- `.env` contents
- API keys
- tokens
- cookies
- SSH keys
- private relay keys
- account credentials

It is acceptable to store environment variable names, non-secret examples, configuration purpose, error types, and decision rationale.

## Templates

For copy-ready structures, read `templates.md` (in the same directory as this file) only when creating or reorganizing memory-bank files.
