# Tasks: toolshelf-and-plugin-settings

Goal: create toolshelf and per-plugin settings so scion, artificer, graver load only configured tools.
Target: `.` = home-repo root (scion: `tools/scion`, artificer: `tools/artificer`, graver: `tools/graver` = `~/.config/opencode/plugins/<name>`; toolshelf: `tools/toolshelf`)
Change: toolshelf-and-plugin-settings

Notes: scion agents/skills are generated from `templates/` (never hand-edit). Old plugins are NOT deleted. Test runner: `bun test` (bun is used by the plugins, bun.lock).

## Dependency Graph
- Batch 1: T01
- Batch 2: T02
- Batch 3 (parallel): T03 T04 T05 T06 T07
- Batch 4 (scion, parallel): T08 T09 T10 T11
- Batch 5 (scion): T12 (tools loader), then T13 (index wiring)
- Batch 6 (artificer T14-T19, graver T20-T25; same internal order, parallel across plugins)
- Batch 7 (docs, parallel): T26 T27 T28
- Batch 8 (REQUIRES EXPLICIT USER CONFIRMATION): T29 T30 T31

- [ ] T01
- File: `tools/toolshelf/README.md`
- Test: `git -C tools/toolshelf rev-parse`
- Depends: -
- Covers: toolshelf/layout
- Do: git init the project (own repo, NOT a plugin: no manifest, not in plugins.csv). README: purpose, per-tool layout, tool.ts contract, how plugins copy via build:tools.
- Verify: `git -C /home/archuser/projects/home-repo/tools/toolshelf rev-parse --git-dir`
- Commit: `docs: add toolshelf README`

- [ ] T02
- File: `toolshelf/scripts/check-toolshelf.mjs`
- Test: `toolshelf/scripts/check-toolshelf.test.mjs`
- Depends: T01
- Covers: toolshelf/layout
- Do: Validator: each tool dir has only skills/agents/rules/tool.ts; SKILL.md frontmatter has name+description; exits non-zero otherwise.
- Verify: `bun test scripts/`
- Commit: `feat: add layout checker`

- [ ] T03
- File: `toolshelf/fnox-secrets/ (skills/fnox-secrets (+scripts), rules/secrets.md from the fnox-secrets plugin)`
- Test: `node scripts/check-toolshelf.mjs fnox-secrets`
- Depends: T02
- Covers: toolshelf/tools
- Do: Copy content from the old plugin unchanged (old plugin stays). Rules secrets.md text kept verbatim.
- Verify: `node scripts/check-toolshelf.mjs`
- Commit: `feat: add fnox-secrets tool`

- [ ] T04
- File: `toolshelf/kanidm/ (skills/kanidm-oidc (+references), agents/kanidm-admin.md, rules/secrets.md)`
- Test: `node scripts/check-toolshelf.mjs kanidm`
- Depends: T02
- Covers: toolshelf/tools
- Do: Copy content from the old plugin unchanged (old plugin stays). Rules secrets.md text kept verbatim.
- Verify: `node scripts/check-toolshelf.mjs`
- Commit: `feat: add kanidm tool`

- [ ] T05
- File: `toolshelf/rustfs/ (skills/rustfs-s3 (+references), agents/rustfs-admin.md, rules/secrets.md)`
- Test: `node scripts/check-toolshelf.mjs rustfs`
- Depends: T02
- Covers: toolshelf/tools
- Do: Copy content from the old plugin unchanged (old plugin stays). Rules secrets.md text kept verbatim.
- Verify: `node scripts/check-toolshelf.mjs`
- Commit: `feat: add rustfs tool`

- [ ] T06
- File: `toolshelf/ssh-agent/ (skills/ssh-agent, tool.ts exporting ssh_load + shell hook + rule (from plugins/ssh-agent/src/index.ts))`
- Test: `node scripts/check-toolshelf.mjs ssh-agent`
- Depends: T02
- Covers: toolshelf/tools
- Do: Copy content from the old plugin unchanged (old plugin stays). Rules secrets.md text kept verbatim.
- Verify: `node scripts/check-toolshelf.mjs`
- Commit: `feat: add ssh-agent tool`

- [ ] T07
- File: `toolshelf/step-ca/ (skills/step-ca-trust, rules/secrets.md)`
- Test: `node scripts/check-toolshelf.mjs step-ca`
- Depends: T02
- Covers: toolshelf/tools
- Do: Copy content from the old plugin unchanged (old plugin stays). Rules secrets.md text kept verbatim.
- Verify: `node scripts/check-toolshelf.mjs`
- Commit: `feat: add step-ca tool`

- [ ] T08
- File: `src/settings.ts`
- Test: `test/settings.test.ts`
- Depends: T03-T07 (batch 3)
- Covers: plugin-settings/lookup,merge,warnings
- Do: Load `scion.jsonc` (or .json) from the 3 locations in order, strip JSONC comments, deep-merge, return role->tool map; warn on invalid entries, never throw.
- Verify: `bun test`
- Commit: `feat(scion): src/settings.ts`

- [ ] T09
- File: `scion.schema.json`
- Test: `test/schema.test.ts`
- Depends: T03-T07 (batch 3)
- Covers: plugin-settings/schema
- Do: JSON schema: `tools` object, string values, free role keys, documented roles secrets, ssh. Test validates an example settings file.
- Verify: `bun test`
- Commit: `feat(scion): scion.schema.json`

- [ ] T10
- File: `tools.list.json`
- Test: `test/tools-list.test.ts`
- Depends: T03-T07 (batch 3)
- Covers: toolshelf/delivery
- Do: List of tool names to copy: fnox-secrets, ssh-agent.
- Verify: `bun test`
- Commit: `feat(scion): tools.list.json`

- [ ] T11
- File: `scripts/build-tools.ts`
- Test: `test/build-tools.test.ts`
- Depends: T03-T07 (batch 3)
- Covers: toolshelf/delivery
- Do: Copy listed tool dirs from toolshelf (path arg/env) into `tools/<name>/`, replacing old copy; add `build:tools` to package.json scripts (same task, one-line edit).
- Verify: `bun test`
- Commit: `feat(scion): scripts/build-tools.ts`

- [ ] T12
- File: `src/tools.ts`
- Test: `test/tools.test.ts`
- Depends: T03-T07 (batch 3), T8
- Covers: tool-loading/*
- Do: Register only configured tools: skills via ctx.skill.transform, agents via ctx.agent.transform, rules via ctx.session.hook("context"), tool.ts via ctx.tool.transform. Missing dir: warn, skip. FIRST confirm V2 signatures against @opencode/plugin types.
- Verify: `bun test`
- Commit: `feat(scion): src/tools.ts`

- [ ] T13
- File: `src/index.ts`
- Test: `test/index.test.ts`
- Depends: T8, T12
- Covers: tool-loading/startup
- Do: Call settings + tools loader inside setup; test with a fake ctx that only configured tools register.
- Verify: `bun test`
- Commit: `feat(scion): src/index.ts`

- [ ] T14
- File: `artificer/src/settings.ts`
- Test: `artificer/test/settings.test.ts`
- Depends: T13
- Covers: plugin-settings/lookup,merge,warnings
- Do: Load `artificer.jsonc` (or .json) from the 3 locations in order, strip JSONC comments, deep-merge, return role->tool map; warn on invalid entries, never throw.
- Verify: `bun test`
- Commit: `feat(artificer): src/settings.ts`

- [ ] T15
- File: `artificer/artificer.schema.json`
- Test: `artificer/test/schema.test.ts`
- Depends: T13
- Covers: plugin-settings/schema
- Do: JSON schema: `tools` object, string values, free role keys, documented roles secrets, ssh. Test validates an example settings file.
- Verify: `bun test`
- Commit: `feat(artificer): artificer.schema.json`

- [ ] T16
- File: `artificer/tools.list.json`
- Test: `artificer/test/tools-list.test.ts`
- Depends: T13
- Covers: toolshelf/delivery
- Do: List of tool names to copy: fnox-secrets, ssh-agent.
- Verify: `bun test`
- Commit: `feat(artificer): tools.list.json`

- [ ] T17
- File: `artificer/scripts/build-tools.ts`
- Test: `artificer/test/build-tools.test.ts`
- Depends: T13
- Covers: toolshelf/delivery
- Do: Copy listed tool dirs from toolshelf (path arg/env) into `tools/<name>/`, replacing old copy; add `build:tools` to package.json scripts (same task, one-line edit).
- Verify: `bun test`
- Commit: `feat(artificer): scripts/build-tools.ts`

- [ ] T18
- File: `artificer/src/tools.ts`
- Test: `artificer/test/tools.test.ts`
- Depends: T13, T14
- Covers: tool-loading/*
- Do: Register only configured tools: skills via ctx.skill.transform, agents via ctx.agent.transform, rules via ctx.session.hook("context"), tool.ts via ctx.tool.transform. Missing dir: warn, skip.
- Verify: `bun test`
- Commit: `feat(artificer): src/tools.ts`

- [ ] T19
- File: `artificer/src/index.ts`
- Test: `artificer/test/index.test.ts`
- Depends: T14, T18
- Covers: tool-loading/startup
- Do: Call settings + tools loader inside setup; test with a fake ctx that only configured tools register.
- Verify: `bun test`
- Commit: `feat(artificer): src/index.ts`

- [ ] T20
- File: `graver/src/settings.ts`
- Test: `graver/test/settings.test.ts`
- Depends: T13
- Covers: plugin-settings/lookup,merge,warnings
- Do: Load `graver.jsonc` (or .json) from the 3 locations in order, strip JSONC comments, deep-merge, return role->tool map; warn on invalid entries, never throw.
- Verify: `bun test`
- Commit: `feat(graver): src/settings.ts`

- [ ] T21
- File: `graver/graver.schema.json`
- Test: `graver/test/schema.test.ts`
- Depends: T13
- Covers: plugin-settings/schema
- Do: JSON schema: `tools` object, string values, free role keys, documented roles secrets, ssh, certificate-manager, sso, s3. Test validates an example settings file.
- Verify: `bun test`
- Commit: `feat(graver): graver.schema.json`

- [ ] T22
- File: `graver/tools.list.json`
- Test: `graver/test/tools-list.test.ts`
- Depends: T13
- Covers: toolshelf/delivery
- Do: List of tool names to copy: fnox-secrets, ssh-agent, step-ca, kanidm, rustfs.
- Verify: `bun test`
- Commit: `feat(graver): tools.list.json`

- [ ] T23
- File: `graver/scripts/build-tools.ts`
- Test: `graver/test/build-tools.test.ts`
- Depends: T13
- Covers: toolshelf/delivery
- Do: Copy listed tool dirs from toolshelf (path arg/env) into `tools/<name>/`, replacing old copy; add `build:tools` to package.json scripts (same task, one-line edit).
- Verify: `bun test`
- Commit: `feat(graver): scripts/build-tools.ts`

- [ ] T24
- File: `graver/src/tools.ts`
- Test: `graver/test/tools.test.ts`
- Depends: T13, T20
- Covers: tool-loading/*
- Do: Register only configured tools: skills via ctx.skill.transform, agents via ctx.agent.transform, rules via ctx.session.hook("context"), tool.ts via ctx.tool.transform. Missing dir: warn, skip.
- Verify: `bun test`
- Commit: `feat(graver): src/tools.ts`

- [ ] T25
- File: `graver/src/index.ts`
- Test: `graver/test/index.test.ts`
- Depends: T20, T24
- Covers: tool-loading/startup
- Do: Call settings + tools loader inside setup; test with a fake ctx that only configured tools register.
- Verify: `bun test`
- Commit: `feat(graver): src/index.ts`

- [ ] T26
- File: `scion/README.md`
- Test: `grep -q build:tools scion/README.md`
- Depends: T13
- Covers: docs
- Do: Document settings file, lookup order, roles, build:tools.
- Verify: `grep build:tools README.md`
- Commit: `docs(scion): settings and tools`

- [ ] T27
- File: `artificer/README.md`
- Test: `grep -q build:tools artificer/README.md`
- Depends: T25
- Covers: docs
- Do: Document settings file, lookup order, roles, build:tools.
- Verify: `grep build:tools README.md`
- Commit: `docs(artificer): settings and tools`

- [ ] T28
- File: `graver/README.md`
- Test: `grep -q build:tools graver/README.md`
- Depends: T25
- Covers: docs
- Do: Document settings file, lookup order, roles, build:tools.
- Verify: `grep build:tools README.md`
- Commit: `docs(graver): settings and tools`

- [ ] T29
- File: `collection/project-stack-collection/AGENTS.md`
- Test: `grep -L Secrets (file absent)`
- Depends: T28
- Covers: cleanup
- Do: REQUIRES EXPLICIT USER CONFIRMATION before execution (tooling definitions are protected). Delete the Secrets section; file is then empty, remove it.
- Verify: `test ! -e collection/project-stack-collection/AGENTS.md`
- Commit: `chore: drop secrets section`

- [ ] T30
- File: `collection/docker-stack-collection/AGENTS.md`
- Test: `grep -c fnox AGENTS.md == 0`
- Depends: T28
- Covers: cleanup
- Do: REQUIRES EXPLICIT USER CONFIRMATION before execution (tooling definitions are protected). In 'Secrets and infra values' remove fnox-secrets plugin/skill mentions, the 'Admin agent / skill' column and the 'Plugins and skills refer to roles...' sentence; KEEP the role -> project/keys table. In 'Host safety' replace 'commands from the kanidm-oidc and rustfs-s3 skills' with 'commands from the skills of the configured tools'.
- Verify: `! grep -n fnox collection/docker-stack-collection/AGENTS.md`
- Commit: `chore: generalize secrets and host safety text`

- [ ] T31
- File: `.opencode/skills/scion-work/SKILL.md`
- Test: `! grep -n fnox .opencode/skills/scion-work/SKILL.md`
- Depends: T28
- Covers: cleanup
- Do: REQUIRES EXPLICIT USER CONFIRMATION before execution (tooling definitions are protected). Remove the fnox-secrets bullet (lines 9-11).
- Verify: `! grep -n fnox .opencode/skills/scion-work/SKILL.md`
- Commit: `chore: remove fnox bullet`
