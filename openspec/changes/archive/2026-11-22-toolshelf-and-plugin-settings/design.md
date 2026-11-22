## Problem Statement
Tool content (secrets, ssh, certificates, SSO, S3) lives in five separate OpenCode plugins. They all load everywhere, so every project sees every tool. scion, artificer and graver need only the tools they are configured for.

## Constraints
- OpenCode terms only: plugin, skill, agent, command, rule, MCP server.
- No runtime dependency on node_modules; the copy is committed.
- Old plugins are not deleted (user decides later).
- Generated scion agents/skills come from `templates/` via `npm run build:agents`; never hand-edit.

## Approach
`toolshelf` is a plain git project (not a plugin) with one self-contained dir per tool. `npm run build:tools` in each plugin copies the tools named in `tools.list.json` into `tools/<name>/`. At startup the plugin reads its settings file (`<plugin>.jsonc`), takes the role -> tool-name map and registers only those tools.

## Decisions & Alternatives
1. Toolshelf is not a plugin. Rejected: each tool as a separate OpenCode plugin (they all show in the plugin list and load everywhere).
2. Delivery by committed copy. Rejected: npm dependency (unconfirmed that OpenCode installs deps for local plugins; skills in node_modules are not discovered).
3. Settings hold only role -> tool name. Rejected: listing individual skills or agents (user rejected; tool name only).
4. Name `toolshelf`. Rejected: `tool-collection` (user rejected).
5. Copy to `tools/<name>/`, outside `skills/`, so the plugin's own skill loader does not auto-register unconfigured tools.
6. Settings lookup layered, later wins: `$XDG_CONFIG_HOME/opencode` (or `~/.config/opencode`), `$OPENCODE_CONFIG_DIR`, nearest `.opencode/` upward from `ctx.directory`. Scalars override, objects deep-merge. Accept `.json` if no `.jsonc`.
7. Collisions of skill ids between plugins and version drift between copies are accepted (last wins).

## Architecture / Components
- toolshelf: `<tool>/{skills,agents,rules,tool.ts}`; `scripts/check-toolshelf` validator.
- per plugin: `tools.list.json`, `scripts/build-tools.ts`, `src/settings.ts`, `<plugin>.schema.json`, `src/tools.ts`, wiring in `src/index.ts`.
- Registration (V2): skills `ctx.skill.transform` (`editor.add` id,name,description,path,content); agents `ctx.agent.transform`; rules `ctx.session.hook("context")` pushing `e.system`; tool modules `ctx.tool.transform`. `tool.ts` default-exports `async setup(ctx): Promise<{rules?: string[]}>` (ssh-agent: `ssh_load` tool, `shell.hook("create.before")` presetting SSH_AUTH_SOCK, rule text).

## Data Flow
build time: tools.list.json -> copy from toolshelf -> `tools/<name>/` (committed). Startup: load+merge settings -> validate (warn) -> for each role's tool name, if `tools/<name>/` exists register its parts.

## Error Handling
Invalid JSONC, unknown tool name, non-string value, missing dir: log a warning, skip that entry, continue. Never throw from setup.

## Testing Strategy
Unit tests per module with temp dirs (settings merge/order, unknown tool warning, loader registers only configured tools with fake ctx, build script copies listed tools). Toolshelf validator checks layout.

## Open Questions
- V2 signatures above come from the request and were NOT re-verified with scion-researcher; the first implementation task must confirm them against `@opencode/plugin` 2.0.21 types.
- Toolshelf git remote unknown; no plugins.csv entry.
- Test runner: `bun test` (bun already used by the plugins).
- While old and new plugins coexist, duplicate rules/skill ids may appear (accepted).
