# root-wiring Specification

## Purpose

TBD - created by archiving change toolshelf-and-plugin-settings. Update Purpose after archive.

## Requirements

### Requirement: Root docs do not hard-code tool plugins
Root and collection instruction files (`AGENTS.md`, `scion-work` skill) SHALL NOT name specific tool plugins as the way to get secrets or SSH; they SHALL refer to the role (secrets, ssh) configured in the plugin settings.

#### Scenario: Collection AGENTS.md
- **GIVEN** a collection `AGENTS.md`
- **WHEN** it describes secrets or SSH access
- **THEN** it refers to the configured role, not to a named plugin
