## Why

Secrets, SSH, certificate-manager, SSO and S3 helpers live in five separate global OpenCode plugins. They show in the plugin list, load for every project, and projects hard-code which tool fills which role in AGENTS.md. We want one place for these tool contents and a per-plugin setting that says which tool fills which role.

## Sub-project plans

- toolshelf -> tools/toolshelf/openspec/changes/toolshelf-and-plugin-settings/ — new project with five tools
- scion -> .opencode/global-plugins/scion/openspec/changes/toolshelf-and-plugin-settings/ — settings, tool list, loading
- artificer -> .opencode/global-plugins/artificer/openspec/changes/toolshelf-and-plugin-settings/ — same for artificer
- graver -> .opencode/global-plugins/graver/openspec/changes/toolshelf-and-plugin-settings/ — same for graver

## What Changes

- Root: remove hard-coded tool-plugin references from collection AGENTS.md files and the scion-work skill (cleanup T29-T31).

## Capabilities

### New Capabilities
- `root-wiring`

## Impact

home-repo root docs only; work lives in the sub-project plans above.
