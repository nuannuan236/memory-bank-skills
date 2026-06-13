# Memory Bank Skills

[中文](README.zh-CN.md) | English

Lightweight skills for maintaining project memory banks with human-reviewable files.

The core idea is simple:

- Keep current facts short.
- Separate stable knowledge from history.
- Mark stale memory instead of letting old notes look active.
- Read memory by need, not by default.
- Never store raw secrets.

This is not an automatic memory system and is not a replacement for vector memory or agent databases. It is a conservative workflow for keeping project context clean, portable, and easy to audit.

## Included Skills

### Claude Code

Path:

```text
claude/memory-bank/
```

This version is command-shaped. It supports action categories such as `status`, `init`, `update`, and `archive`, and is designed for Claude-style skill or slash-command workflows.

### Codex

Path:

```text
codex/memory-bank-maintainer/
```

This version is natural-language-shaped. It is designed for Codex skills and focuses on recognizing requests such as "update memory", "organize the memory bank", "archive old context", and "mark stale knowledge".

## Recommended Memory Structure

Start small:

```text
memory-bank/
  current.md
  knowledge.md
  history/
    YYYY-MM.md
```

Only add more files when the project needs them:

```text
memory-bank/
  current.md
  knowledge.md
  commands.md
  contracts.md
  decisions.md
  pitfalls.md
  history/
    YYYY-MM.md
```

## Safety Rule

Never store raw secrets in memory files:

- `.env` contents
- API keys
- tokens
- cookies
- SSH keys
- private relay keys
- account credentials

It is acceptable to store environment variable names, configuration purpose, non-secret examples, error types, and decision rationale.

## Example

See `examples/minimal-memory-bank/` for a small before-and-after conversion from one append-only `memory.md` file into layered memory files.

## Install

Copy the skill directory for your agent into the relevant local skills folder.

For Claude-style usage:

```text
claude/memory-bank/ -> .claude/skills/memory-bank/
```

For Codex-style usage:

```text
codex/memory-bank-maintainer/ -> .codex/skills/memory-bank-maintainer/
```

Use the repository version directly if your agent supports loading skills from project-local folders.

## License

MIT
