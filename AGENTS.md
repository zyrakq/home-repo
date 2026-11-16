# home-repo

## Language

- Always write file contents (code, docs, configs, commit messages) in English.
- Chat replies: use the language the user writes in.

## Destructive operations — NEVER without explicit permission

- NEVER delete, `rm -rf`, move-away-and-delete, or otherwise destroy a git
  repository (or any directory containing one) without the user's explicit
  permission in the current conversation. "Move" means `mv`, never
  clone-then-delete.
- This applies even when deletion seems like an obvious step of the requested
  task — ask first, always.
- Prefer preserving originals: copy/clone first, verify the copy, and only
  remove the original after the user confirms.
