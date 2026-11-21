---
name: artificer-work
description: Rules for working on collection projects with the artificer plugin. Use when using artificer on projects under collection/project-stack-collection.
---

# Artificer work

- The `artificer` plugin is global (`.opencode/global-plugins/artificer`).
- Work in `collection/project-stack-collection` and treat it as the project
  root. Specs go into the `openspec/` of the specific sub-project, not the
  collection.
- Secrets solution: the `fnox-secrets` plugin and its `fnox-secrets` skill
  (also covers SSH access to private git repos). Load the skill BEFORE asking
  the user for any credential.
