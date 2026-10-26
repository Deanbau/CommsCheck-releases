# How it works

## The pieces

- **`server.py`** is the only thing that talks to the antennas and the only thing that holds the admin password. It polls every 2 seconds, builds the state, writes the event log, and serves the web page.
- **`web/`** is the page (React, TypeScript, Tailwind). The built copy is in `web/dist`, so running the dashboard needs only Python.
- **`snapshot.py`, `insights.py`, `headsets.py`, `meter.py`, `notify.py`** hold the logic for setup snapshots, run-time, reports and diagnosis (firmware, antenna balance, flapping), headset check in/out, the audio meter, and push alerts. They are plain functions and are tested without an antenna.
- **`mobile.html`** is the phone page.
- **`electron/`** is the desktop app. It opens the dashboard in one window and starts `server.py` itself.
- **`data/`** is where saved things go (see below).

## Talking to the Bolero

The Bolero's own web page uses a private REST API. The dashboard uses the same one. It is undocumented and could change with a firmware update. This was learned on firmware 3.5.1-14.

- **Reads** use the built-in `user:userPass` account the Riedel page uses.
- **Writes** use HTTP Basic as `admin`, with the password from `BOLERO_ADMIN_PASSWORD`.
- **Config writes arrive a moment late.** A write returns straight away but shows up on a read 2 to 3 seconds later. The dashboard allows for this.

What each thing is called:

| Thing | Where it comes from |
|---|---|
| Live pack status (battery, signal, firmware) | `nodeStatus`, per antenna |
| Which antenna a pack is on | `audioPorts` (a type 1 port per pack, with a `nodeId`) |
| Pack config, keys, audio | `bp` |
| Partylines, profiles | `pl`, `profiles` |
| Audio channel and NSA names | `audioChannels`, `ioDeviceConfigs` |
| Registration | `netSettings`, field `registrationMode` |

Things to know about it:

- The DECT master lists every pack in its own `nodeStatus`. That list is not the packs on that antenna. Use `audioPorts` for that.
- Signal is a 4-step level (0 excellent to 3 poor, 255 while a pack is joining). Battery is a percentage (-128 while a pack is joining). The dashboard treats those two as unknown.
- A pack's config contains a live `audioPorts` list that the antennas disagree about. The dashboard ignores it when checking for changes.
- A pack's `changeRights` is a 32-bit mask of what the wearer may change, one bit per feature (a set bit means allowed). "Full menu locked" writes 0. The main menu is bit 29.
- Flashing an antenna is `POST locate` with `{active: 1 or 0, nodes: [{id: nodeId}]}` (true/false and plain ids give a 400), and the antenna reports whether it is flashing, so that one can be read back.
- Identify (`locate`) is a toggle. The second call turns it off. Nothing reports its state, so the dashboard keeps its own timer.
- A key can point at a partyline (port type 2), a 2/4-wire channel (3), an input (5), an output (6) or a beltpack (1, with the pack's internal id).
- The antenna's WebSocket pushes status about every 4 seconds, which is no faster than polling, so the dashboard polls.
- The antenna list comes from any one antenna that answers. The list is cached for a few seconds, so a new antenna shows up within a poll or two.

## Battery run-time

A pack's battery has a charge in mAh (about 4800 when full) and a percentage. The percentage is the same number the Riedel page shows. Time left is the charge divided by how fast the pack is draining.

- **Measured:** after 5 minutes of readings (one a minute), the dashboard fits a straight line through the last half hour of charge readings. That tells it this pack's real drain. Nothing is shown for a flat line or a pack on USB power.
- **Typical:** before that, it divides the charge by 155 mA. That figure comes from the time-left each pack showed on its own screen. Four lightly used packs all worked out at 153 to 158 mA, and the estimate lands within 3% of what they displayed. Heavier packs drain more: one that was roaming and talking a lot read 286 mA, a comms pack 190 mA. So the typical figure is the optimistic end until the measured one takes over.

Reference readings (pack's own screen, against its charge when read):

| Pack | Charge (mAh) | Shown on the pack | Drain |
|---|---|---|---|
| lightly used | 3140 | 20h 07m | 156 mA |
| lightly used | 3456 | 21h 52m | 158 mA |
| lightly used | 3624 | 23h 33m | 154 mA |
| lightly used | 3171 | 20h 12m | 157 mA |
| a bit busier | 3056 | 17h 52m | 171 mA |
| comms pack | 3744 | 19h 42m | 190 mA |
| roaming, talking | 4316 | 15h 06m | 286 mA |

## The audio meter

The antenna has no audio levels. The NSA announces its stream over SAP (multicast address 239.255.255.255, port 9875), saying which group, port and format it uses. The server joins that stream (24-bit, 48 kHz, 6 channels), measures each channel about four times a second, and sends the levels to the page. Silence means the watched channel's peak stays under the set level for the set time (default -60 dBFS for 3 minutes). The machine running the server has to be on the audio network to receive the multicast.

## Routes

The page talks to `server.py` only through routes it lists. Routes added for the current release:

- `GET /api/pack-trends?id=<pack id>&hours=<n>`: one pack's battery and signal samples (6 hours by default, up to 24), and its drops and moves.
- `GET /api/stored` and `POST /api/stored`: saved mic profiles and layout presets.
- `POST /api/headset`: headset check out, returned, not returned, or an edit. `POST /api/check-mode`: the check in/out switch.
- `POST /api/bp/<id>/name`: rename a pack on the antenna. It needs the admin password.

The pack and antenna hover cards read the same state the page already has. They fetch nothing extra.

## Saved data

Saved things live in `data/`, which git ignores because it holds crew names. The exception is `profiles.json`, which goes in the user folder (see below).

| File | Holds |
|---|---|
| `log.jsonl` | the event log, brought back after a restart |
| `samples.jsonl` | one reading per pack per minute: battery, signal, antenna (for reports, run-time and trends) |
| `snapshot.json` and `snapshot-<time>.json` | the saved setup, and a dated copy of each one saved |
| `checkout.json` | the pack board |
| `headsets.json` | headset records: which headset went out with which pack, when it came back, and any marked not returned |
| `checkmode.json` | whether headset check in/out is on, so a restart keeps it |
| `profilecolors.json` | the colour given to each profile. A colour, once given, stays |
| `meter.json` | which audio streams are shown, alerted and which channel |
| `alertpacks.json` | the alert packs: on or off, severity, which packs |
| `connection.json` | the antenna address you connected to (never the password) |
| `mypack.json` | which beltpack is the comms tech's own |
| `showmode.json` | whether show mode is on, so a restart keeps it |
| `telegram.json` | the Telegram bot token and chat, if you chose to remember them (readable by your user only) |
| `rights.json` | each locked pack's earlier access, so unlocking can restore it |

`profiles.json` holds saved mic profiles and layout presets. It goes in the folder named by `BOLERO_DATA_DIR` if that is set. If not, it goes in your user folder: `~/Library/Application Support/Comms Check` on a Mac, `%APPDATA%\Comms Check` on Windows, and `~/.config/comms-check` on Linux. The desktop app sets `BOLERO_DATA_DIR` to its own data folder, so there the file sits with the others.

The desktop app also keeps `admin.bin` in its own app data folder, if you chose to remember the admin password. It is the password encrypted by Electron's safeStorage (the system keychain). It is not in `data/`.

Deleting a file just resets that feature. `BOLERO_DATA_DIR` points the server at a different folder, which is useful for trying things out without touching the real log and snapshots.

## Safety rules

- **Only listed routes exist.** There is no pass-through to the antenna. Every request body is checked: known fields only, numbers inside the Riedel limits, and anything else is refused.
- **Writes to the antenna need the admin password.** With `--lan` they also need `BOLERO_WRITE_TOKEN`. Reads have no login, so use `--lan` only on a closed network.
- **A few writes change only the dashboard's own files:** headset records, the check in/out switch, saved mic profiles and layouts, and profile colours. They need no antenna password. They still need the local check, and with `--lan` the write token.
- **Nothing destructive is offered.** The app can't deregister a pack or delete a partyline.
- **Show mode is enforced by the server.** While it is on, risky routes are refused (403) or answered with a 409 that carries a plain description; the page asks the person and repeats the request with a confirmation flag. A request without that flag never reaches the antenna, whichever page or script sent it.
- **Big changes make you confirm.** Duplicate asks you to type the pack number, and restore asks you to type `RESTORE`. Both show a dry run first.
- **The admin password is kept in memory in a browser.** It is typed into the page, sent to the local server, checked against the antenna, and never written to disk or the log. It is gone when the server stops. The desktop app can keep it, but only if you tick "Remember it in the keychain": then it is encrypted with the system keychain into `admin.bin`, never shown or logged, and "Forget the saved admin password" in the Server menu deletes it.
- **Connecting is guarded.** Antenna addresses must be private IPv4. With `--lan`, connecting and the password need the write token, so nobody else on the network can point the dashboard elsewhere.
- **Phone alerts are opt-in.** Nothing is sent until you set up Telegram, or set `BOLERO_WEBHOOK_URL` for a webhook, and tick Send alerts. The token is never returned to the page, never logged, and never in an error message.

## Tests

`python3 test_server.py` checks all of the above logic with made-up data in a throwaway folder. It never touches `data/`. Its checks include headset check in/out, the firmware, antenna balance and flapping checks, saved mic profiles and layout presets, fixed profile colours, Show mode, and the Telegram set-up. `cd web && npm run build` type-checks the page.

## Not confirmed on a live system

Applying keys, audio and the duplicate wizard through the dashboard, a real snapshot restore, the battery run-time estimate, and Telegram or webhook delivery. The same writes work when made from the antenna's own page, but the dashboard's own routes for them have only been tested with made-up data.

Headset check in/out, the diagnosis checks (firmware, antenna balance, flapping and "likely flat"), the keychain save, the dock bounce, the splash screen and new-antenna discovery have only been tested against a simulator. [`docs/live-check.md`](live-check.md) is the plan for a session on real hardware.
