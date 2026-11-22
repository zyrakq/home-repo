## ADDED Requirements

### Requirement: Build-time copy of tool dirs
Each plugin SHALL provide `npm run build:tools` that copies tool directories from toolshelf into the plugin's `tools/<name>/` (outside `skills/`, so OpenCode does not auto-discover them). The copied `tools/` MUST be committed, so there is no runtime dependency on toolshelf or node_modules. The set of tools to copy is the plugin's documented tool list in `tools.list.json`.

#### Scenario: Build copies tools
- **GIVEN** toolshelf with the five tools and graver's `tools.list.json` naming all five
- **WHEN** `npm run build:tools` runs in graver
- **THEN** `graver/tools/<name>/` mirrors each tool dir and stale dirs not in the list are removed

#### Scenario: Missing toolshelf
- **GIVEN** toolshelf is not at the expected path
- **WHEN** `npm run build:tools` runs
- **THEN** it exits non-zero with a message naming the path, and `tools/` is untouched

### Requirement: Register only configured tools
At startup the plugin SHALL read its settings and register the full content (skills, agents, rules, tool module) of exactly the tool dirs named in `tools` values; unnamed tools MUST NOT be registered. Skills via `ctx.skill.transform` (`editor.add` with id, name, description, path, content), agents via `ctx.agent.transform`, rules via `ctx.session.hook("context")` pushing `e.system` text, tool modules via `ctx.tool.transform` through the module's `setup`.

#### Scenario: Only configured tool active
- **GIVEN** `{"tools":{"secrets":"fnox-secrets"}}` and a copied `tools/kanidm/`
- **WHEN** the plugin starts
- **THEN** skill `fnox-secrets` is registered and skill `kanidm-oidc` and agent `kanidm-admin` are not

#### Scenario: Rules injected
- **GIVEN** a configured tool with `rules/secrets.md`
- **WHEN** the session context hook fires
- **THEN** the rule text is pushed to the system prompt

#### Scenario: Tool module run
- **GIVEN** `{"tools":{"ssh":"ssh-agent"}}`
- **WHEN** the plugin starts
- **THEN** `ssh-agent/tool.ts` `setup` runs and the `ssh_load` tool is registered

### Requirement: Unknown or absent tool warns
A role naming a tool dir that is not in `tools/` SHALL produce a warning and be skipped; startup MUST continue.

#### Scenario: Unknown tool name
- **GIVEN** `{"tools":{"sso":"nope"}}`
- **WHEN** the plugin starts
- **THEN** a warning names `nope` and other tools still register

### Requirement: Role to tool resolution lives only in settings
After this change, no AGENTS.md or scion-work rule SHALL name the fnox-secrets plugin or skill as the secrets solution; the role -> project/keys data table in docker-stack-collection stays.

#### Scenario: Cleanup applied
- **GIVEN** the confirmed cleanup
- **WHEN** the three protected files are grepped for `fnox-secrets`
- **THEN** no match remains in them
