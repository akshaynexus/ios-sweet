# iOSOnAnd tracker

[iOSOnAnd](https://hackios.appaiproxy.com) is a community tracker of which
Android phones run iOS ports and what works on each. Every area of a phone
(boot, wifi, gpu, touch, audio, ...) has a state, a one-line detail and a
source link.

This port's page: https://hackios.appaiproxy.com/device/a71 (device slug `a71`).

The tracker is updated with a small command-line client, `cli.mjs` (Node 18
or newer, no dependencies). This file covers setup and the commands this
repo uses. `node ~/.local/share/iosonand/cli.mjs --help` lists all of them.

## One-time setup

Keep the CLI outside the repo:

```sh
mkdir -p ~/.local/share/iosonand
curl -fsSLo ~/.local/share/iosonand/cli.mjs https://hackios.appaiproxy.com/cli.mjs
```

Point it at the live tracker (without this it talks to `http://localhost:3000`).
Add this to your shell profile:

```sh
export TRACKER_URL=https://hackios.appaiproxy.com
```

Optional, to shorten the commands below:

```sh
alias tracker='node ~/.local/share/iosonand/cli.mjs'
```

Check that it works:

```sh
node ~/.local/share/iosonand/cli.mjs check
```

## Signing in

Either of these works. Neither puts anything in the repo.

1. GitHub sign-in through `gh` (needs `gh auth login` done once):

   ```sh
   node ~/.local/share/iosonand/cli.mjs login
   node ~/.local/share/iosonand/cli.mjs whoami
   ```

   `login` proves your GitHub account with a short-lived secret gist, then
   deletes it. The tracker token is saved in `~/.config/iosonand/tokens.json`.
   `whoami` should list `a71` among the devices you maintain.

2. An API key. Make one at https://hackios.appaiproxy.com/profile/keys with
   the scopes you need (`status:write` for states, `media:write` for photos,
   `fork:write` for fork records), or with the CLI:

   ```sh
   node ~/.local/share/iosonand/cli.mjs keys-create --name a71-agent --scopes status:write,media:write --expires-days 90
   ```

   The secret is printed once. Put it in `TRACKER_TOKEN`, either in your
   shell profile (`export TRACKER_TOKEN=ion_...`) or in a `.env` file at the
   repo root, which `.gitignore` excludes. The CLI does not read `.env`
   itself, so load it into the shell first:

   ```sh
   set -a; . ./.env; set +a
   ```

`node ~/.local/share/iosonand/cli.mjs tokens` shows which token is in use,
with the value masked.

Writes to `a71` need an account that maintains it. If `status-set` answers
403, ask to maintain it with `claim-add a71 --note "..."`, or claim the repo
with `repo-claim OWNER/REPO --device a71`.

## Reading the current state

No sign-in needed:

```sh
node ~/.local/share/iosonand/cli.mjs status-get --device a71
node ~/.local/share/iosonand/cli.mjs status-get --device a71 --area wifi
node ~/.local/share/iosonand/cli.mjs areas
```

`areas` prints the exact area slugs. The ones that apply to this port:

| Area | Slug |
|---|---|
| Boot to Home Screen | `boot` |
| Touch + buttons | `touch` |
| GPU compositing (Adreno 618) | `gpu` |
| Wi-Fi | `wifi` |
| Bluetooth | `bluetooth` |
| Cellular modem | `modem` |
| Audio output | `audio` |
| Camera capture | `camera` |
| Battery reporting | `battery` |
| Auto-brightness | `brightness` |
| Sensors (general) | `sensors` |
| Haptics | `haptics` |
| Keyboard input | `keyboard` |
| SpringBoard stability | `stability` |
| Thermals under load | `thermal` |
| Video encode (HEVC) | `video-encode` |
| Phone calls, SMS, Face ID, passcode, Wallet | `calls`, `sms`, `face-id`, `passcode`, `wallet` |

`ipc`, `kernel-mem` and `return-to-oneui` are OneiOS-only areas.

## Recording a change

The states are `works`, `in-progress`, `broken` and `unknown`; the CLI
rejects anything else. Every write needs `--detail` (what the build, log or
test showed) and `--source` (a commit, CI run, log or PR URL).

```sh
node ~/.local/share/iosonand/cli.mjs status-set <device> <area> <state> --detail TEXT --source REF
```

Examples for this port (replace the SHA, run ID and text with the real ones):

```sh
# Boot reached the lock screen on the handset with a new kernel
node ~/.local/share/iosonand/cli.mjs status-set a71 boot works \
  --detail "Kernel 0085 boots to the lock screen on SM-A715F; swipe unlock reaches the home screen" \
  --source https://github.com/dmyslisz/ios-a71/commit/<sha>

# Wi-Fi firmware loads and scans, but the interface does not come up
node ~/.local/share/iosonand/cli.mjs status-set a71 wifi in-progress \
  --detail "WLAN firmware boots and scan returns networks; interface start is rejected (boot log)" \
  --source https://github.com/dmyslisz/ios-a71/actions/runs/<run-id>

# A change broke something that worked before
node ~/.local/share/iosonand/cli.mjs status-set a71 touch broken \
  --detail "After <sha> every I2C probe to 0x48 times out (err=60); no touch events" \
  --source https://github.com/dmyslisz/ios-a71/commit/<sha>
```

Read back what you wrote with `status-get --device a71 --area <slug>`.

## Posting photos and clips

A photo update shows on the phone's page with its text (maintainers, up to
10 files; images up to 15 MB, videos up to 95 MB):

```sh
node ~/.local/share/iosonand/cli.mjs update a71 home-screen.jpg touch.mp4 \
  --text "Home screen after kernel 0085; touch scrolling in Settings" --area touch
node ~/.local/share/iosonand/cli.mjs updates a71
```

Crop or blur anything that shows an IMEI, serial number or personal data
before you upload it.

## Working offline

When the tracker cannot be reached, queue the write instead of losing it:

```sh
node ~/.local/share/iosonand/cli.mjs queue status-set a71 wifi in-progress \
  --detail "..." --source https://github.com/dmyslisz/ios-a71/commit/<sha>
```

Queued commands are appended to `./TRACKER_QUEUE.md` in the current
directory (`.gitignore` excludes it). Send them later, in order:

```sh
node ~/.local/share/iosonand/cli.mjs flush
```

Sent lines are removed; failed ones stay in the file. Do not put `--token`
on a queued command: it would be written into the queue file in plain text.

## Honesty rules

- Record only what a build, log, test or capture shows, and cite it in
  `--source`.
- What has not been shown on the handset stays `unknown`. Code that builds
  but has not run on the phone is at most `in-progress`.
- Never lower someone else's state without evidence that it no longer holds.
- Report the result you saw, not the one you expect: a guess is not a
  `works`.
- Keys and tokens never go in git, in a commit, or in a `--detail` text.
