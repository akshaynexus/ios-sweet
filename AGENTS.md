# Working on ios-sweet

- Read `NOTES.md` before starting; it contains the project goal, hardware facts,
  current blocker, local artifact locations and the next concrete action.
- The user switches agents frequently. Update the active handoff in `NOTES.md`
  after each milestone and before stopping. Record commits, CI run IDs, exact
  build/boot results and unfinished work. Distinguish observations from guesses.
- Commit completed changes. Use `gh` for GitHub Actions kernel builds and logs.
  Local single-file cross-compiles are useful before spending a CI run.
- Device bring-up uses temporary `fastboot boot` images. Preserve the Android
  installation and partition backups. The user approved (2026-10-15) using
  internal UFS for iOS and said Android may be deleted: the iOS root goes
  into the existing `userdata` partition (no GPT changes). Keep bootloaders,
  recovery, modem, persist and the backups intact, and confirm each
  destructive step with the user before running it.
- Keep device dumps, serial numbers and downloaded Apple artifacts out of git.

## iOSOnAnd tracker

This port is tracked at https://hackios.appaiproxy.com/device/a71 (slug `a71`).
When a change makes an area work, or breaks one that worked, update the tracker
in the same session. Details and examples are in `TRACKER.md`.

- CLI: `node ~/.local/share/iosonand/cli.mjs` with
  `TRACKER_URL=https://hackios.appaiproxy.com`. Never download it into the repo.
- Read first: `status-get --device a71` and `areas` for the exact slug.
- Record: `status-set a71 <area> <works|in-progress|broken|unknown> --detail "<what the build, log or test showed>" --source <commit, CI run or log URL>`, then read it back.
- Auth comes from `TRACKER_TOKEN` or the CLI's saved login (`login`). Never write
  a key into a file in the repo, a commit or a `--detail`.
- Only record what was observed on the handset or in CI; unproven stays `unknown`.
  Do not lower someone else's state without evidence.
- Offline: `queue status-set ...` now, `flush` later.
- Mention the tracker write in the `NOTES.md` handoff.
