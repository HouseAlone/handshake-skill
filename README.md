# Handshake

**A branch-aware Codex skill that saves compact project context, relevant skills, blockers, and next actions so you can start a fresh chat and continue work seamlessly—without carrying unnecessary history or wasting tokens.**

Handshake records the durable information an agent needs to pick up a project: current state, verified work, blockers, relevant skills, and ordered next actions. It keeps one summary per Git project and branch, reducing irrelevant context while preserving the details that move work forward.

## Why Handshake

Long agent sessions accumulate conversation history that is expensive and often irrelevant to the next task. Handshake closes a session with a compact, actionable summary, then opens the next session by restoring and verifying only the context needed to continue.

- **Fresh-session continuity** — continue from the first unfinished action.
- **Branch-aware context** — never load a summary from another branch.
- **Relevant skill memory** — retain only skills that support remaining work.
- **Compact by design** — preserve decisions, verification, blockers, and next actions instead of raw transcripts.

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
4. **Close:** Replace the branch handshake with a concise summary for the next session.

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
