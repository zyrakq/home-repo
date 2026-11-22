## Why

Secrets, SSH, certificate-manager, SSO and S3 helpers live in five separate global OpenCode plugins. They show in the plugin list, load for every project, and projects hard-code which tool fills which role in AGENTS.md. We want one place for these tool contents and a per-plugin setting that says which tool fills which role.

## What Changes

- New project `toolshelf` (own git repo at `tools/toolshelf`, not an OpenCode plugin): one self-contained directory per tool (`fnox-secrets`, `kanidm`, `rustfs`, `ssh-agent`, `step-ca`) with skills, agents, rules and an optional tool module, migrated from the existing plugins (old plugins stay untouched).
- scion, artificer and graver each get a settings file (`scion.jsonc`, `artificer.jsonc`, `graver.jsonc`) holding only a role -> tool-name map, with JSON schema and validation warnings.
- Each plugin copies the configured-capable tool dirs from toolshelf into its own `tools/<name>/` at build time (`npm run build:tools`, committed) and at startup registers only the tools named in settings: skills, agents, rules and tool modules.
- Protected AGENTS.md / scion-work cleanup (needs explicit user confirmation): role -> tool resolution moves to the settings files.

## Impact

- New repo: `tools/toolshelf`.
- Plugins: scion, artificer, graver (`src/`, `scripts/`, `tools/`, `package.json`, schema).
- Protected files (confirmation required): `collection/project-stack-collection/AGENTS.md`, `collection/docker-stack-collection/AGENTS.md`, `.opencode/skills/scion-work/SKILL.md`.
- Not changed: the old plugins `fnox-secrets`, `kanidm`, `rustfs`, `ssh-agent`, `step-ca` (the user removes them later). While both exist, same skill ids register twice (last wins, accepted).
- Path note: scion is at `~/.config/opencode/plugins/scion` = `.opencode/global-plugins/scion` in home-repo; `tools/scion` does not exist. The same holds for artificer and graver.
