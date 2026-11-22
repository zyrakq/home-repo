## ADDED Requirements

### Requirement: Toolshelf is a plain content repo
The toolshelf project SHALL be a git repo that is not an OpenCode plugin: it MUST NOT contain a plugin entry point or manifest that OpenCode could load, and MUST NOT be listed in plugins.csv.

#### Scenario: Not loadable as a plugin
- **GIVEN** the toolshelf repo
- **WHEN** its root is scanned for `package.json` `exports`/`main` and `src/index.ts`
- **THEN** none exist

### Requirement: One self-contained directory per tool
Toolshelf SHALL hold one directory per tool (`fnox-secrets`, `kanidm`, `rustfs`, `ssh-agent`, `step-ca`) whose content uses plugin layout: `skills/<id>/SKILL.md`, optional `agents/*.md`, `rules/*.md`, and an optional `tool.ts` module. A tool dir MUST NOT reference files outside itself.

#### Scenario: Migrated content present
- **GIVEN** the five tools migrated
- **WHEN** the check script runs
- **THEN** `fnox-secrets` has skill `fnox-secrets` with its `scripts/`, `kanidm` has skill `kanidm-oidc`, agent `kanidm-admin` and rule `secrets.md`, `rustfs` has skill `rustfs-s3`, agent `rustfs-admin` and rule `secrets.md`, `ssh-agent` has skill `ssh-agent` and `tool.ts`, `step-ca` has skill `step-ca-trust` and rule `secrets.md`

#### Scenario: Broken tool dir detected
- **GIVEN** a tool dir whose SKILL.md lacks `description`
- **WHEN** the check script runs
- **THEN** it exits non-zero and names the file

### Requirement: Tool module contract
A `tool.ts` module SHALL default-export `async function setup(ctx): Promise<{ rules?: string[] }>` that registers its OpenCode tools and hooks through `ctx` and returns extra rule texts for the system prompt.

#### Scenario: ssh-agent module
- **GIVEN** `ssh-agent/tool.ts`
- **WHEN** a plugin calls its `setup(ctx)`
- **THEN** the tool `ssh_load` is registered, `SSH_AUTH_SOCK` is preset for shell commands, and the returned rules mention the socket path
