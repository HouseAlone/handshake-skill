# Handshake Skill

A branch-aware Codex skill that saves compact project context, relevant skills, blockers, and next actions so you can start a fresh chat and continue work seamlessly—without carrying unnecessary history or wasting tokens.

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
