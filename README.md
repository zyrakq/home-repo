# home-repo

Monorepo catalog for hosting other mono-projects. Each subproject is an
independent git repository cloned into its own folder and tracked in the
`projects.csv` manifest (`path;url` format).

## Structure

- `collection/` — collection subprojects (cloned repos, not committed)
- `projects.csv` — manifest of subprojects
- `.githooks/` — `project-sync.sh` + `post-merge` / `post-checkout`:
  missing repos are cloned automatically

## Hook setup

```sh
git config core.hooksPath .githooks
```

## Adding a subproject

Add a `path;url` line to `projects.csv` and run
`.githooks/project-sync.sh`.
