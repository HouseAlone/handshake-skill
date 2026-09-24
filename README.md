# What is Handshake?

Handshake is an open-source **session handoff and context management skill** for **Codex** and **Claude Code**.

It helps AI coding agents continue software projects across fresh sessions by generating a compact project handoff instead of relying on long conversation histories.

Each handoff preserves the durable information needed to resume work immediately, including project state, architecture decisions, relevant files, active Git branch, blockers, and next steps.

The result is faster onboarding, lower token usage, and more consistent AI-assisted software development.

## Why Handshake?

AI coding sessions naturally grow over time. As conversations become longer, starting a fresh session often means copying large amounts of context or manually rebuilding the project's state.

Handshake replaces this workflow with a structured, branch-aware handoff that contains only the information required to continue development.

Instead of transferring entire conversations, developers transfer durable project knowledge.

This makes new Codex or Claude Code sessions faster to start, easier to understand, and significantly more token-efficient.

- **Fresh-session continuity** — continue from the first unfinished action.
- **Branch-aware context** — never load a summary from another branch.
- **Relevant skill memory** — retain only skills that support remaining work.
- **Rolling context** — recycle verified architecture, file anchors, decisions, and skill memory; refresh only the state that changed.

## How Handshake works

```text
┌───────────────────────┐
│   AI Coding Session   │
│  Codex / Claude Code  │
└───────────┬───────────┘
            │
            ▼
   $handshake close
            │
            ▼
┌───────────────────────┐
│   Compact Handoff     │
│                       │
│ • Project state       │
│ • Relevant files      │
│ • Decisions           │
│ • Git branch          │
│ • Blockers            │
│ • Next steps          │
└───────────┬───────────┘
            │
            ▼
┌───────────────────────┐
│   Fresh AI Session    │
│  Codex / Claude Code  │
└───────────┬───────────┘
            │
            ▼
      $handshake
            │
            ▼
┌───────────────────────┐
│ Continue development  │
│ without rebuilding    │
│ project context       │
└───────────────────────┘
```
## Compatibility

| Environment | Installation | Invocation |
| --- | --- | --- |
| Codex | Copy `handshake/` to `~/.codex/skills/handshake/` | `$handshake` or `$handshake close` |
| Claude Code | Load this repository as a plugin | `/handshake:handshake` or `/handshake:handshake close` |

The saved handshake is shared across compatible installations on the same computer:

```text
~/codex-session-handshakes/<project-key>/handshake-<branch>.md
```

## Install for Codex

Copy the standalone skill into your personal Codex skills directory:

```bash
cp -R handshake ~/.codex/skills/handshake
```

Restart Codex if the skill does not appear automatically.

## Use in Codex

Open a project session and continue from its saved context:

```text
$handshake
```

Close the current session and replace its compact continuation context:

```text
$handshake close
```

## Use as a Claude Code Plugin

Test the plugin directly from a local clone:

```bash
claude --plugin-dir .
```

Then use the namespaced skill:

```text
/handshake:handshake
/handshake:handshake close
```

The repository includes the required `.claude-plugin/plugin.json` manifest and `skills/handshake/` package. It can be submitted to the Claude Code community marketplace after validation.

## How It Works

1. **Open:** Locate the current Git project and branch, then read that branch's handshake file if it exists.
2. **Verify:** Compare the saved state with the actual repository before continuing.
3. **Continue:** Start the first unresolved next action unless a blocker or decision requires input.
4. **Close:** Update the rolling branch handshake: preserve verified durable context, refresh changed state, and retain at most three immediate next steps.

## Repository Layout

```text
handshake/                         # Standalone Codex skill
.claude-plugin/plugin.json         # Claude Code plugin manifest
skills/handshake/                  # Claude Code plugin skill
LICENSE                            # MIT
```

## Contributing

Issues and pull requests are welcome. Keep changes focused on reliable cross-session continuity and compact, durable project context.

## License

[MIT](LICENSE)
