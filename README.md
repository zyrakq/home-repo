# home-repo

Monorepo catalog for hosting other mono-projects. Each subproject is an
independent git repository cloned into its own folder and tracked in the
`projects.csv` manifest (`path;url` format).

## Structure

- `collection/` — collection subprojects (cloned repos, not committed)
- `projects.csv` — manifest of subprojects
- `plugins.csv` — manifest of OpenCode plugins (`path;url`)
- `.githooks/` — `project-sync.sh`, `plugin-sync.sh` + `post-merge` /
  `post-checkout`: missing repos are cloned automatically

## Hook setup

```sh
git config core.hooksPath .githooks   # REQUIRED — enables auto-clone hooks
.githooks/plugin-sync.sh              # clone all plugins listed in plugins.csv
```

Plugins are independent git repos. Global plugins live in
`.opencode/global-plugins/<name>` (symlink to `~/.config/opencode/plugins`);
update one with `git -C .opencode/global-plugins/<name> pull`, then `/reload`
in OpenCode.

## Adding a subproject

Add a `path;url` line to `projects.csv` and run
`.githooks/project-sync.sh`.
