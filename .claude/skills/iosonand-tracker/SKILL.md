---
name: iosonand-tracker
description: Update the iOSOnAnd tracker (hackios.appaiproxy.com, device a71) with evidence. Use when a change to this port makes a phone feature work (boot, touch, gpu, wifi, audio, ...) or breaks one, or when asked to sync, update or push progress to the tracker.
---

# Update the iOSOnAnd tracker for a71

Device slug: `a71`. Page: https://hackios.appaiproxy.com/device/a71.
Full guide: `TRACKER.md` at the repo root.

Below, `T` stands for `node ~/.local/share/iosonand/cli.mjs`, run with
`TRACKER_URL=https://hackios.appaiproxy.com` set.

1. **CLI.** If `~/.local/share/iosonand/cli.mjs` is missing, fetch it there
   (never into the repo):
   `mkdir -p ~/.local/share/iosonand && curl -fsSLo ~/.local/share/iosonand/cli.mjs https://hackios.appaiproxy.com/cli.mjs`.
   Run `T check`.
2. **Auth.** `T whoami` must show a login that maintains `a71`. The token
   comes from `TRACKER_TOKEN` or the CLI's saved login. If neither exists,
   ask the user to run `T login` (GitHub via `gh`) or to set `TRACKER_TOKEN`.
   Never open, print, create or commit a file that holds a key.
3. **Current state.** `T status-get --device a71`. Note each area's state,
   detail and source.
4. **Map the change to areas.** `T areas` for the exact slugs. Typical ones
   here: `boot` (lock or home screen reached), `touch` (S6SY771 input),
   `gpu` (Adreno 618 / GMU), `wifi`, `bluetooth`, `audio`, `modem`, `camera`,
   `battery`, `stability`, `thermal`. Pick the state from the evidence:
   - `works`: shown working on the handset (log, capture, owner's test);
   - `in-progress`: partly working, or built but not yet run on the phone;
   - `broken`: shown failing, or a regression;
   - `unknown`: nothing proven. Do not upgrade on a guess, and do not lower
     someone else's state without evidence.
5. **Record.** One call per area:
   `T status-set a71 <area> <state> --detail "<what the build, log or test showed>" --source <commit, CI run, log or PR URL>`.
   Keep the detail factual and short; no keys, serials or IMEIs.
   Read it back: `T status-get --device a71 --area <area>`.
   If the tracker is unreachable: `T queue status-set ...` (writes
   `./TRACKER_QUEUE.md`, git-ignored), then `T flush` later. Never put
   `--token` on a queued command.
6. **Media (optional).** For a photo or clip that shows the result:
   `T update a71 <file> [<file> ...] --text "what changed" --area <area>`.
   Crop out IMEI, serial numbers and personal data first.
7. **Report back.** Tell the user each area changed (old state to new),
   the detail and source used, anything queued, and any 401/403 with the
   CLI's hint. Mention the update in the `NOTES.md` handoff.
