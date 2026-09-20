# Handshake Skill

A compact, branch-scoped session handoff skill for Codex. It saves one concise Markdown summary per project and branch, then loads it in a later chat so work can resume with the goal, current state, verification, blockers, and next actions intact.

## Install

Copy the `handshake` directory into your Codex skills directory:

```bash
cp -R handshake ~/.codex/skills/handshake
```

Restart Codex if the skill does not appear automatically.

## Use

Start or resume a project session:

```text
$handshake
```

At the end of a session, save the compact continuation context:

```text
$handshake close
```

The saved file lives outside the repository, under:

```text
~/codex-session-handshakes/<project-key>/handshake-<branch>.md
```

Different branches keep separate context. Closing a session replaces only that branch's prior handshake.
