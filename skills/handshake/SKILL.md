---
name: handshake
description: "Open or close a branch-scoped project handshake that preserves compact context, relevant skills, unresolved work, and ordered next actions. Use when starting or ending a Claude Code work session."
---

# Handshake

Use this skill to move between Claude Code sessions without carrying an entire previous conversation. It saves one compact summary per project and branch, then uses it to continue unfinished work in a fresh session.

## Invocation

- `/handshake:handshake` opens the current project and branch handshake.
- `/handshake:handshake close` saves the current state for the next session.

## Core Rule

Treat handshakes as scoped to both project and branch. Do not use a global latest handshake. The project identity is the nearest Git root when available; otherwise it is the current working directory. A handshake from another branch must not be loaded.

Use `scripts/session_handshake.py` for file discovery and path creation. It stores one handshake per branch under:

```text
~/codex-session-handshakes/<project-key>/handshake-<branch>.md
```

## Mode Resolution

Opening this skill without `close` means **open**. Run the open flow immediately; do not ask the user whether this is the start or end of a session.

Only use the close flow when the user explicitly includes `close`, or clearly asks to close, save, summarize, or end the session. This prevents a newly opened chat from overwriting the compact context it needs to load.

## At Session Start

First run:

```bash
python3 <skill-dir>/scripts/session_handshake.py open --cwd <current-working-directory>
```

If a handshake is found, read it before doing project work. Use it as context, then verify against the actual repository/files before making changes. Read the **Relevant Skill Memory** section before continuing. Use a listed skill only when it applies to the next action; do not load every past skill merely because it was recorded. Give a compact continuation briefing: the goal, where work stopped, what was verified, blockers, relevant skills, and the ordered next actions. Then continue directly with the first unresolved next action. Do not ask the user to confirm opening the session.

Pause only when the handshake records a blocker, an open question that materially affects the next action, or no remaining action. Say exactly what is needed in that case.

If no handshake is found, say that no compact context exists for this project and branch yet and continue normally. Do not imply that the contents of another chat were recovered; inspect the repository to establish current state instead.

## At Session End

Create a concise Markdown handshake for the current project and branch. First run:

```bash
python3 <skill-dir>/scripts/session_handshake.py close --cwd <current-working-directory>
```

The script prints the exact file path to write. Replace that branch's existing handshake at this path.

The handshake should include only durable context that helps the next session:

- Goal
- Current state
- Important files and symbols
- Commands run and verification status
- Decisions and constraints
- Known issues or blockers
- Relevant skill memory
- Ordered next actions
- Open questions

At close, turn any unfinished work into an actionable continuation list. Each item must say what to do, why it remains, and the relevant file, command, or decision when known. Put the immediate next action first. Include only work that still needs attention; omit completed work. If nothing remains, state that explicitly.

Also record a **Relevant Skill Memory** section. List only skills used in this session that will materially help complete an unfinished next action. For each, include its exact invocation name, which next action it supports, and a brief reason. Remove skills that are no longer useful, and write `None` when no skill needs to carry forward. If a listed skill is unavailable in the new session, mention that only when it blocks the next action; never install it automatically.

Avoid dumping raw logs, long transcripts, or large diffs unless the next session truly needs them. Prefer exact file paths, commands, commit/branch names, and error messages over broad narrative.

## Good Handshake Shape

```markdown
# Session Handshake: <project-name>

- Project: `<absolute project path>`
- Branch: `<branch or unknown>`
- Created: `<ISO timestamp>`

## Goal

## Current State

## Important Files

## Verification

## Decisions and Constraints

## Relevant Skill Memory

- `$<skill-name>` — Supports next action <number>: <why it is useful>.

## Next Actions

1. **<action>** — Why it remains: <reason>. Context: `<file>`, `<command>`, or <decision>.
2. **<action>** — Why it remains: <reason>. Context: <context>.

## Open Questions
```
