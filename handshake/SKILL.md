---
name: handshake
description: "Open or close a Codex work session with one compact, branch-scoped handshake that records the current state, unresolved work, and ordered next actions."
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

A bare `$handshake` means **open**. Run the open flow immediately; do not ask the user whether this is the start or end of a session.

Only use the close flow when the user explicitly says `$handshake close`, or clearly asks to close, save, summarize, or end the session. This prevents a newly opened chat from overwriting the compact context it needs to load.

## At Session Start

When the user invokes this skill at the beginning of work, first run:

```bash
python3 <skill-dir>/scripts/session_handshake.py open --cwd <current-working-directory>
```

If a handshake is found, read it before doing project work. Read its sections in this order: **Macro Vision & Architectural Context**, **File Anchors**, **Relevant Skill Memory**, then **Next Actions**. Inspect the anchored files first and verify the saved state against the actual repository before making changes; do not scan broad directories unless the anchors are insufficient. Use a listed skill only when it applies to the next action; do not load every past skill merely because it was recorded. Give a compact continuation briefing: the goal, where work stopped, the macro context, what was verified, blockers, relevant skills, and the ordered next actions. Then continue directly with the first unresolved next action. Do not ask the user to confirm opening the session.

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
- Macro vision and architectural context
- File anchors
- Commands run and verification status
- Decisions and constraints
- Known issues or blockers
- Relevant skill memory
- Up to three ordered, immediate next actions
- A compact pointer to deferred work, when applicable
- Open questions

At close, write **Macro Vision & Architectural Context** as no more than three short bullets: the business purpose, the relevant system boundary or cross-language flow, and an architectural invariant that must not be broken. Write **File Anchors** with three to eight paths relative to the repository root. For each anchor, name the relevant symbol or component and why the next session needs it. Include only direct source dependencies and essential configuration; never use whole directories as anchors.

Turn unfinished work into an actionable continuation list of at most three sequential, immediate steps. Each item must say what to do, why it remains, and the relevant file, command, or decision when known. Put the immediate next action first. Omit completed work and do not embed a long backlog. If further work exists, record only one compact **Deferred Work** pointer to its issue, ticket, backlog file, or `None`. If nothing remains, state that explicitly.

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

## Macro Vision & Architectural Context

- Business purpose: <one concise line>
- System boundary or flow: <one concise line>
- Do not break: <one concise line>

## File Anchors

- `<repository-relative-path>` — `<symbol or component>`: <why this is needed next>.

## Verification

## Decisions and Constraints

## Relevant Skill Memory

- `$<skill-name>` — Supports next action <number>: <why it is useful>.

## Next Actions

1. **<action>** — Why it remains: <reason>. Context: `<file>`, `<command>`, or <decision>.
2. **<action>** — Why it remains: <reason>. Context: <context>.
3. **<action>** — Why it remains: <reason>. Context: <context>.

## Deferred Work

- `<issue, ticket, or backlog path>` — or `None`.

## Open Questions
```
