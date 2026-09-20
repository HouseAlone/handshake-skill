---
name: handshake
description: "Resume or close a Codex work session with one compact, branch-scoped handshake that records the current state, unresolved work, and ordered next actions."
---

# Session Handshake

Use this skill when the user wants to start from a previous handshake, prepare one for the next session, or keep long-running work cheap by carrying a compact project-and-branch-specific summary instead of an entire chat.

## Core Rule

Treat handshakes as scoped to both project and branch. Do not use a global latest handshake. The project identity is the nearest Git root when available; otherwise it is the current working directory. A handshake from another branch must not be loaded.

Use `scripts/session_handshake.py` for file discovery and path creation. It stores one handshake per branch under:

```text
~/codex-session-handshakes/<project-key>/handshake-<branch>.md
```

## Mode Resolution

A bare `$handshake` means **resume**. Run the resume flow immediately; do not ask the user whether this is the start or end of a session.

Only use the close flow when the user explicitly says `$handshake close`, or clearly asks to close, save, summarize, or end the session. This prevents a newly opened chat from overwriting the compact context it needs to load.

## At Session Start

When the user invokes this skill at the beginning of work, first run:

```bash
python3 <skill-dir>/scripts/session_handshake.py resume --cwd <current-working-directory>
```

If a handshake is found, read it before doing project work. Use it as context, then verify against the actual repository/files before making changes. Give a compact continuation briefing: the goal, where work stopped, what was verified, blockers, and the ordered next actions. Then continue directly with the first unresolved next action. Do not ask the user to confirm resuming.

Pause only when the handshake records a blocker, an open question that materially affects the next action, or no remaining action. Say exactly what is needed in that case.

If no handshake is found, say that no compact context exists for this project and branch yet and continue normally. Do not imply that the contents of another chat were recovered; inspect the repository to establish current state instead.

## At Session End

When the user invokes this skill to close, summarize, or end a session, create a concise Markdown handshake for the current project and branch. First run:

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
- Ordered next actions
- Open questions

At close, turn any unfinished work into an actionable continuation list. Each item must say what to do, why it remains, and the relevant file, command, or decision when known. Put the immediate next action first. Include only work that still needs attention; omit completed work. If nothing remains, state that explicitly.

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

## Next Actions

1. **<action>** — Why it remains: <reason>. Context: `<file>`, `<command>`, or <decision>.
2. **<action>** — Why it remains: <reason>. Context: <context>.

## Open Questions
```
