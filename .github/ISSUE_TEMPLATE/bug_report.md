---
name: Bug report
about: Report something that is broken or incorrect in the skill
title: "[bug] "
labels: bug
assignees: ''
---

## What happened

A clear and concise description of what went wrong.

## Trigger phrase

What did you (or the user) say in chat that should have loaded the skill?

```
@browser screenshot the dashboard
```

## Expected behavior

What did you expect the agent to do?

## Actual behavior

What did the agent do instead? Include the agent's reply verbatim if possible.

## Environment

| Item | Value |
|------|-------|
| VS Code version | e.g. 1.95.0 |
| OS | e.g. Windows 11 23H2 |
| Claude version (if known) | e.g. Claude Code 1.0.x |
| Skill version | e.g. 1.0.0 (see `CHANGELOG.md`) |
| `workbench.browser.enableChatTools` setting | `true` / `false` |

## Minimal reproduction

If possible, list the exact steps to reproduce the issue:

1. Place a copy of the skill at `<path>`
2. Open VS Code and start a Claude chat session
3. Say: `<trigger phrase>`
4. Observe: `<what happens>`

## Screenshots / logs

If applicable, attach screenshots or paste relevant log output.

## Possible fix

If you have an idea of what the fix is, link to the relevant section of `SKILL.md` and describe the change.
