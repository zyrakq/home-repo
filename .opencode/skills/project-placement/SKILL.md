---
name: project-placement
description: Where to create a new project in home-repo and when a new category is allowed. Use before creating, cloning or moving any project or plugin repo.
---

# Project placement

## Hard rule

- Create projects only inside home-repo or inside an already registered
  subproject. Never in `~/projects/other`, `~/Documents`, `/tmp` or any other
  outside path.
- `/tmp/opencode` is for throwaway files only, never for projects.

## Registration

- OpenCode plugin: row in `plugins.csv`.
- Anything else: row in `projects.csv` (`path;url`).

## Categories

A category is defined by the project's role, not its technology.

- `collection/` — sets of stacks and similar collections.
- `tools/` — shared tooling used by other projects.

## Decision order

1. An existing category fits by role: use it.
2. The project belongs to the area of an existing project: nest it there.
3. Otherwise put it temporarily in the closest category by role.
4. In doubt: do not choose, ask the user and propose a location.

## New category

- Only after the user's explicit confirmation.
- Needs a distinct role not covered by existing categories AND 2+ projects
  (or a clear prospect). One project of a new type does not create a category.
- Create it in one commit: folder with `.gitkeep`, `.gitignore` rule
  (`<dir>/*/` + `!<dir>/.gitkeep`), README line, and a criterion line in this
  skill.
