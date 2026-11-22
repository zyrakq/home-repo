# Tasks: toolshelf-and-plugin-settings (root)

Goal: root-level cleanup after the sub-project changes land.
Target: `.` = home-repo root
Change: toolshelf-and-plugin-settings

Ordering: sub-project plans first (toolshelf, then scion/artificer/graver), then this root cleanup. T29-T31 need explicit user confirmation.

- [x] T29
- File: `collection/project-stack-collection/AGENTS.md`
- Test: `grep -L Secrets (file absent)`
- Depends: T28
- Covers: cleanup
- Do: REQUIRES EXPLICIT USER CONFIRMATION before execution (tooling definitions are protected). Delete the Secrets section; file is then empty, remove it.
- Verify: `test ! -e collection/project-stack-collection/AGENTS.md`
- Commit: `chore: drop secrets section`

- [x] T30
- File: `collection/docker-stack-collection/AGENTS.md`
- Test: `grep -c fnox AGENTS.md == 0`
- Depends: T28
- Covers: cleanup
- Do: REQUIRES EXPLICIT USER CONFIRMATION before execution (tooling definitions are protected). In 'Secrets and infra values' remove fnox-secrets plugin/skill mentions, the 'Admin agent / skill' column and the 'Plugins and skills refer to roles...' sentence; KEEP the role -> project/keys table. In 'Host safety' replace 'commands from the kanidm-oidc and rustfs-s3 skills' with 'commands from the skills of the configured tools'.
- Verify: `! grep -n fnox collection/docker-stack-collection/AGENTS.md`
- Commit: `chore: generalize secrets and host safety text`

- [x] T31
- File: `.opencode/skills/scion-work/SKILL.md`
- Test: `! grep -n fnox .opencode/skills/scion-work/SKILL.md`
- Depends: T28
- Covers: cleanup
- Do: REQUIRES EXPLICIT USER CONFIRMATION before execution (tooling definitions are protected). Remove the fnox-secrets bullet (lines 9-11).
- Verify: `! grep -n fnox .opencode/skills/scion-work/SKILL.md`
- Commit: `chore: remove fnox bullet`
