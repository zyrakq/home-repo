---
session: ses_e1fb
updated: 2026-11-16T18:41:15.067Z
---

## Objective
- Initialize `project-stack-collection` in home-repo's `collection/`: a catalog of projects from `/home/archuser/projects` with git + plugin wiring (hooks, `projects.csv`, `plugins.csv`); a follow-up question on how the sync script works, then push to GitHub and adjust remotes.

## Requirements
- Stack tree: `stacks/{apps/{argiago,velora,open-coreui}, ui/{open-velaui}, auth/{kanidm-admin,nostr-oidc-bridge,wcauth-tb-component}, certs/{certivore,step-ca-companion}, archive/argiago-react}`.
- open-coreui (backend with closed frontend) goes in `apps/`; open-velaui (an alternative implementation of the closed frontend) goes in `ui/`.
- Projects are copied (not cloned) to preserve `.env` and local files.
- Commits follow the git-commit skill format (English, what/why, header ≤50 chars).
- The `projects.csv` manifest traditionally uses HTTPS URLs; local project remotes may be SSH — the script does not touch existing clones.

## Decisions
- `projects.csv` uses HTTPS URLs; the collection's own origin was switched from SSH to HTTPS (`https://github.com/zyrakq/project-stack-collection.git`) because SSH push fails (`Permission denied (publickey)`).
- On user confirmation: `stacks/archive/argiago-react` must point to `git@github.com:zyrakq/argiago-react.git` — the local remote was fixed.

## Work State
### Completed
- The script was explained: it reads `projects.csv` lines after the header (`path;url`), skips empty path / `-` / an already existing `.git`, otherwise runs `git clone "$url"`; existing repos are left alone.
- The collection's origin was switched to HTTPS: `git remote set-url origin https://github.com/zyrakq/project-stack-collection.git` (verified with `git remote -v`).
- The local remote of `stacks/archive/argiago-react` was fixed to `git@github.com:zyrakq/argiago-react.git` (verified).

### Active
- The collection push is still not done.

### Blocked
- `git push -u origin master` over HTTPS: `fatal: could not read Username for 'https://github.com': No such device or address` — private repo, no credentials (`gh auth status`: not logged in; credential helper not configured); interactive password input is unavailable. Needs `gh auth login` or a push from the user's terminal.

## Next Move
1. Update `projects.csv`: line `stacks/archive/argiago-react;https://github.com/zyrakq/argiago.git` → `https://github.com/zyrakq/argiago-react.git` (HTTPS equivalent of the new remote).
2. Check whether the `zyrakq/argiago-react` repo exists on GitHub; commit the manifest change in the git-commit format.
3. Remind the user about pushing: `gh auth login` then `git push -u origin master`, or push from their own terminal.
4. (Optional) Remove the old `argiago-react` line from `/home/archuser/projects/home-repo/projects.csv` if it referenced the old URL.

## Relevant Files
- `/home/archuser/projects/home-repo/collection/project-stack-collection/projects.csv` — manifest (path;url), 10 entries, all HTTPS; the argiago-react line needs a fix.
- `/home/archuser/projects/home-repo/collection/project-stack-collection/.githooks/project-sync.sh` — 9 lines, clones only missing directories.
- `/home/archuser/projects/home-repo/collection/project-stack-collection/README.md` — structure documentation.
- `/home/archuser/projects/home-repo/projects.csv` — home-repo manifest, both collections registered.

## Important Context
- Two commits in the collection: `b7475dd` (initial), `a4eb7c8` (path/URL corrections); the working tree was clean before the argiago-react remote fix (a remote fix creates no file changes).
- Branch is `master`; SSH does not work on this machine at all (publickey); HTTPS without credentials also fails.
- `collection/*/` is gitignored in home-repo; project-stack-collection is an independent git repo inside it.
- The hook script does not update existing clones — `.env` files are safe.
