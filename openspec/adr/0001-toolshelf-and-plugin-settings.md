# 0001. Tools in a plain toolshelf project, selected by plugin settings

**Status:** accepted
**Date:** 2026-11-22
**Change:** openspec/changes/archive/2026-11-22-toolshelf-and-plugin-settings/
**Specs:** openspec/specs/root-wiring/spec.md

## Context
Secrets, SSH, certificate-manager, SSO and S3 helpers lived in five separate global OpenCode plugins. They all load everywhere and show in the plugin list, and projects hard-code which tool fills which role. scion, artificer and graver need only the tools they are configured for.

## Decision
`toolshelf` is a plain git project (not a plugin) with one self-contained dir per tool. `npm run build:tools` in each plugin copies the tools listed in `tools.list.json` into a committed `tools/<name>/`. At startup the plugin reads its settings file (role -> tool name only) and registers only those tools. Settings are layered (global config, `$OPENCODE_CONFIG_DIR`, nearest `.opencode/`), later wins. Work is split into sub-project plans (toolshelf, scion, artificer, graver) plus a root cleanup plan.

## Alternatives considered
- Each tool as a separate OpenCode plugin: all show in the plugin list and load everywhere.
- npm dependency: unconfirmed that OpenCode installs deps for local plugins; skills in node_modules are not discovered.
- Listing individual skills or agents in settings: rejected by the user; tool name only.
- Name `tool-collection`: rejected by the user.

## Consequences
- Root AGENTS.md files and the scion-work skill no longer hard-code tool plugins (root-wiring spec).
- Skill id collisions between plugins and version drift between copies are accepted (last wins).
- Old plugins are not deleted; the user decides later.
