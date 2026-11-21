# home-repo

## Language

- Always write file contents (code, docs, configs, commit messages) in English.
- Chat replies: use the language the user writes in.

## Destructive operations — NEVER without explicit permission

- NEVER delete, `rm -rf`, move-away-and-delete, or otherwise destroy a git
  repository (or any directory containing one) without the user's explicit
  permission in the current conversation. "Move" means `mv`, never
  clone-then-delete.
- This applies even when deletion seems like an obvious step of the requested
  task — ask first, always.
- Prefer preserving originals: copy/clone first, verify the copy, and only
  remove the original after the user confirms.

## Project placement

- NEVER create projects outside home-repo and the projects nested in it.
  Ask before placing a new project.

## Graver work

When working with the graver plugin (stacks, deploy configs), work in
`collection/docker-stack-collection` and treat it as the project root: also
read its `AGENTS.md` and all rules from there (secrets solution, infra roles,
stack conventions).

## On-demand rules

- Before plugin or stack work, load the matching skill from `.opencode/skills`
  (`plugin-manifests-rule` for plugins.csv, `stack-work` for graver and
  collection stacks, `project-placement` before creating or moving any
  project).
