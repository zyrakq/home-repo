## ADDED Requirements

### Requirement: Per-plugin settings file
Each of scion, artificer and graver SHALL read its own JSONC settings file (`scion.jsonc`, `artificer.jsonc`, `graver.jsonc`; `.json` accepted when no `.jsonc`). Lookup directories, merged in this order with later winning: `$XDG_CONFIG_HOME/opencode` (default `~/.config/opencode`), `$OPENCODE_CONFIG_DIR`, the nearest `.opencode/` found by walking up from the session directory. Scalars override, objects deep-merge.

#### Scenario: Project overrides global
- **GIVEN** global `graver.jsonc` with `tools.secrets = "fnox-secrets"` and `tools.s3 = "rustfs"`, and a project `.opencode/graver.jsonc` with `tools.secrets = "other"`
- **WHEN** settings load
- **THEN** `tools.secrets` is `other` and `tools.s3` is `rustfs`

#### Scenario: No settings file
- **GIVEN** no settings file in any location
- **WHEN** settings load
- **THEN** the result has an empty `tools` map and no error

### Requirement: Role to tool-name mapping only
The settings `tools` object SHALL map free-form role keys to tool directory names; it MUST NOT list individual skills or agents. The documented roles are `secrets`, `ssh`, `certificate-manager`, `sso`, `s3`.

#### Scenario: Valid graver settings
- **GIVEN** `{"tools":{"secrets":"fnox-secrets","ssh":"ssh-agent","certificate-manager":"step-ca","sso":"kanidm","s3":"rustfs"}}`
- **WHEN** settings are validated
- **THEN** no warning is produced

#### Scenario: Invalid value
- **GIVEN** `{"tools":{"secrets":42}}`
- **WHEN** settings are validated
- **THEN** a warning names `tools.secrets`, and that entry is ignored

#### Scenario: Unknown top-level key
- **GIVEN** `{"skills":["x"]}`
- **WHEN** settings are validated
- **THEN** a warning names the unknown key `skills`

### Requirement: JSON schema shipped
Each plugin SHALL ship `<plugin>.schema.json` describing the settings; scion and artificer examples list only `secrets` and `ssh`.

#### Scenario: Schema accepts examples
- **GIVEN** the schema and the documented example for the plugin
- **WHEN** the example is checked against the schema
- **THEN** it is valid

### Requirement: Malformed file is non-fatal
A settings file that is not valid JSONC SHALL produce a warning and be skipped; the plugin MUST keep loading.

#### Scenario: Broken JSON
- **GIVEN** a settings file with a syntax error
- **WHEN** settings load
- **THEN** a warning names the file and the other files still merge
