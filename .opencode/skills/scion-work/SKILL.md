---
name: scion-work
description: Rules for working on OpenCode plugins with the scion agent. Use when using scion in this project.
---

# Scion work

- The `scion` plugin is global (`.opencode/global-plugins/scion`).
- Secrets solution: the `fnox-secrets` plugin and its `fnox-secrets` skill
  (also covers SSH access to private git repos). Load the skill BEFORE asking
  the user for any credential. Never put secret values into plugin files.
