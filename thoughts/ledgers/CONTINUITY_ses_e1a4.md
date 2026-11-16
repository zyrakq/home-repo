---
session: ses_e1a4
updated: 2026-11-16T19:06:23.765Z
---

## Objective
- Manage the home-repo monorepo: move the stackbuilder repository into the collection, set up agent rules, commit the changes.

## Requirements
- Move repositories only with `mv`, NEVER clone-then-delete; do not delete a git repository without the user's explicit permission.
- File contents (code, docs, configs, commit messages) are always in English; chat replies use the interlocutor's language.
- Commit messages follow the git-commit skill format: header ≤50 chars (infinitive), what/why blocks separated by a blank line, no bullets, no labels.
- The destructive-operations rule lives in the local AGENTS.md (`/home/archuser/projects/home-repo/AGENTS.md`), not in the global one.

## Decisions
- AGENTS.md must be fully in English — the Russian word "Peremesti" from the Destructive operations section was removed and replaced with `"Move" means mv, never clone-then-delete`.

## Work State
### Completed
- Moved stackbuilder into `collection/project-stack-collection/stacks/tools/stackbuilder` (remote `git@github.com:zyrakq/stackbuilder.git`, branch `master`, commit `bc9c9c5`).
- The collection's `projects.csv` was updated with the line `stacks/tools/stackbuilder;git@github.com:zyrakq/stackbuilder.git` (commit `196f2b7`).
- Local AGENTS.md created: Language + Destructive operations sections, fully in English.
- The original `/home/archuser/projects/tools/stackbuilder` and the empty `/home/archuser/projects/tools` were removed.
- AGENTS.md committed: `2a462ab` "Add agent rules for destructive ops and language" (17 insertions, create mode).

### Active
- (none)

### Blocked
- `git push` is impossible from this environment: `git@github.com: Permission denied (publickey)`, `gh` is unauthenticated. Not pushed: `bc9c9c5` (stackbuilder), `196f2b7` (project-stack-collection), `2a462ab` (home-repo) — the user must push.

## Next Move
1. Wait for user instructions; destructive actions only after explicit confirmation.

## Relevant Files
- `/home/archuser/projects/home-repo/AGENTS.md`: agent rules (Language + Destructive operations), committed `2a462ab`, not pushed.
- `/home/archuser/projects/home-repo/collection/project-stack-collection/stacks/tools/stackbuilder`: new repository location (commit `bc9c9c5` not pushed).
- `/home/archuser/projects/home-repo/collection/project-stack-collection/projects.csv`: manifest with the stackbuilder line (commit `196f2b7` not pushed).

## Important Context
- `project-stack-collection` is a separate git repository inside `collection/` with its own origin (ssh) and `projects.csv`.
- The user reacts sharply negatively to clone-instead-of-move, deleting originals, Russian text in files, and Russian chat replies.
