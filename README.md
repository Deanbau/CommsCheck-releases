# Comms Check

**Free to use. If you found it useful, [donations are welcome](https://www.paypal.com/donate/?business=865HZJWESSNFL&no_recurring=0&currency_code=AUD).**

A dashboard for watching and running a Riedel Bolero standalone intercom during a show: a live map of antennas and beltpacks, an event log with alerts, a pack configurer with drag-and-drop keys and audio settings, and a set of show-day tools. It runs as a desktop app, or as a small server you open in a browser.

> **Unofficial.** Comms Check is not made, endorsed or supported by Riedel Communications and is not connected to them. Riedel and Bolero are trademarks of their owner. See [NOTICE.md](NOTICE.md).

Tested against Bolero firmware 3.5.1-14 in standalone (AES67) mode. It talks to the same undocumented interface the Bolero's own web page uses, so a firmware update could break it, and it can change the settings of a live system. Read [NOTICE.md](NOTICE.md) before using it at an event.

## Screenshots

Taken from the simulator, with generic names. The meters read made-up NSA streams: PGM In from the desk and Talk To Stage going out.

| | |
|---|---|
| ![Map](docs/screenshots/map.png) **Map** | ![Grid](docs/screenshots/grid.png) **Grid** |
| ![Audio meters for NSA streams](docs/screenshots/audio-meters.png) **Audio meters for NSA streams** | ![Silent audio on an NSA stream](docs/screenshots/audio-meters-silent.png) **Silent audio on an NSA stream** |
| ![Silent audio alert](docs/screenshots/silent-audio-alert.png) **Silent audio alert** | ![Audio channels](docs/screenshots/audio-channels.png) **Audio channels** |
| ![Pack keys and rotaries](docs/screenshots/pack-keys.png) **Pack keys and rotaries** | ![Pack audio](docs/screenshots/pack-audio.png) **Pack audio** |
| ![Pack controls](docs/screenshots/pack-controls.png) **Pack controls** | ![Pack quick menu](docs/screenshots/pack-quick-menu.png) **Pack quick menu** |
| ![Battery and signal trends](docs/screenshots/pack-trends.png) **Battery and signal trends** | ![Undo and roll back](docs/screenshots/pack-restore.png) **Undo and roll back** |
| ![Comms matrix](docs/screenshots/comms-matrix.png) **Comms matrix** | ![Partylines](docs/screenshots/partylines.png) **Partylines** |
| ![Profiles](docs/screenshots/profiles.png) **Profiles** | ![Pre-show check](docs/screenshots/pre-show-check.png) **Pre-show check** |
| ![Coverage and battery report](docs/screenshots/coverage-report.png) **Coverage and battery report** | ![Setup snapshots](docs/screenshots/setup-snapshots.png) **Setup snapshots** |

## Support

Comms Check is free and MIT licensed. If you found it useful, [donations are welcome](https://www.paypal.com/donate/?business=865HZJWESSNFL&no_recurring=0&currency_code=AUD).

## Install

Download the latest build from the [Releases page](https://github.com/Deanbau/CommsCheck-releases/releases/latest):

- **Desktop app**: `.dmg` for Apple silicon Macs, `.exe` installer or `.zip` for Windows, `.AppImage` or `.deb` for Linux. It carries its own server, so nothing else is needed.
- **Plain server program**: `CommsCheck-server-<system>.zip` (mac, windows or linux), for a show computer with no desktop window. Unzip it, run `CommsCheck`, and open http://127.0.0.1:8080.
- `SHA256SUMS.txt` to check a download.

The apps are not code-signed, so the first launch needs one extra click (right-click > Open on a Mac, "More info" > "Run anyway" on Windows). If macOS says the app is damaged, run `xattr -dr com.apple.quarantine "/Applications/Comms Check.app"`. Intel Macs are not covered. This repo only holds downloads and documentation; the source is private.

### Desktop app

The app is one window: the dashboard, with the server running inside it. A small splash shows while it starts. It launches its own server (on the next free port if 8080 is busy), shows the dashboard when it is ready, and restarts the server if it crashes. A server panel docks at the bottom (Server menu, or Cmd/Ctrl+Shift+S): pick the **network interface** (this computer only, one adapter, or all), the **port**, and a write token if other computers will use it, then start, stop or restart. The console shows what the server prints, with Copy, Save and Clear. The app keeps the computer awake while the server runs (switch off in the Server menu), can open the dashboard in a browser for other devices, and never saves the write token.

When it opens view-only, it asks once for the admin password. Tick "Remember it in the keychain" and the password is kept encrypted with a key held by the system keychain (the file is `admin.bin` in the app's data folder), and the next start applies it without asking. **Server > Forget the saved admin password** removes it. A critical alert bounces the dock icon (or flashes the taskbar) while the app is in the background.


## What it does

**Watching**
- **Map** of antennas with their packs around them (closer = better signal), or a **grid** of cards. A pack tile is plain unless something is wrong: amber means it needs a look, red means a problem, and the small coloured dot is its zone. Hover a pack to enlarge it and see a card with its battery, signal, antenna and time left; hover an antenna for its packs, temperature, address and firmware. Click a pack to open it; shift-click to select several for bulk Identify and Unlatch. A pack that moves to another antenna slides across with a pulse where it left and where it landed. Antennas are laid out by themselves in equal circles, and fit mode follows new ones as they join; one that stops answering stays as an offline circle. Each antenna has a thin bar along its bottom edge showing how full it is (the limit comes from the antenna). Click an antenna to open its window (Identify, rename, the packs on it, and its pack count and temperature over time). When wireless or NFC registration is open, every antenna gets amber waves and "Wireless open" / "NFC open" chips. Map and grid keep their own zoom and scroll.
- **Problem chips** on the toolbar (Low battery, Weak signal, Offline, each with a count) dim everything else on the map and grid. Pick several to see any of them.
- **Offline packs** wait in a strip along the bottom of the map. Click one to open it.
- **Top bar**: the network name, Live, connected packs with a thin progress bar, antennas (an antenna that has stopped answering shows on the map as offline), the RF word, and a "Registration open" chip when a new pack could register. On the right, the three mode switches (Show mode, Check in/out, Phone alerts) with Unlatch all beside them, the audio meter, a padlock (green open: control is on, red closed: view only) and Settings. Hover the network name for every antenna's address. With no admin password a **View only** button sits in the toolbar.
- **Event log** (saved to disk, so it survives a restart): online and offline, low battery, low coverage, roaming between antennas, hot or worn batteries, hot antennas, firmware that differs from the rest, config changes, registration opening and closing, drift from the saved setup, headset check-outs, and the diagnosis lines below. Filter by type (a drop-down), pack or text, export CSV or JSON. An offline line says which antenna the pack was on, and a pack that drops with under 10% battery is reported as likely flat.
- **Alerts** at the top centre: several at once stack like a deck with the newest in front, and spread out when you hover them; "Clear all" dismisses the lot. Critical ones stay until you dismiss them, warnings fade (with a countdown bar). Optional sound and desktop notification. A pack going offline is critical only in show mode, otherwise a warning. A red frame goes round the window while a critical alert is showing. Several packs dropping off one antenna at once is reported once, naming the antenna. A low-coverage alert has to last about 6 seconds, so a passing blip doesn't fire it.
- **Contact lost**: if no antenna answers for 10 seconds (one missed check is ignored), a huge banner says so, counts how long, lists every antenna with a spinner, and says why it thinks it happened: this computer is not on the antennas' network, the gateway does not answer, the antennas don't answer ping, or they answer ping but not their web page. It works that out by pinging the gateway and each antenna, and a small pill pings 1.1.1.1 to show whether this computer has internet at all (that one ping only happens while contact is lost). While contact is lost the rest of the dashboard is greyed out and cannot be clicked, so nothing is changed on stale information. Only the event log stays live, and a button on the banner opens the connection box to point it at a different antenna. The banner goes away by itself. If the antenna it is connected to stops answering, it carries on through the next one that answers (the admin password is the same across the system), logs the switch, and remembers the new address.
- **Diagnosis**: a **flapping** alert when one pack drops off or changes antenna 4 times in 10 minutes (once per 10 minutes, and not for a burst of packs vanishing together, which is an antenna fault); an **antenna balance** warning when one antenna is at least 80% full while another is at most half full, naming the three weakest packs on the full one to move nearer the quiet one (logged when it starts and when it clears, and shown in the Antenna log and the pre-show check); and a **firmware** check that names the packs and antennas on a different version from the rest (pre-show check, log, and a badge in the pack's window).
- **Audio meter** for the NSA's audio streams, found automatically from the NSA's own announcement, with a "silent for N seconds" alert on each channel you tick to watch (one or several per stream) (default 3 minutes below -60 dBFS, both adjustable). It reports once per silent spell, even across restarts, and re-arms only after audio is heard again. Turn a stream's alert on or off with "Alert on silence" in its panel.
- **Listen**: the headphone button beside the meter, or beside any channel in its panel, plays that channel through the computer, with a volume slider, a Boost setting for quiet feeds (up to +24 dB, with a limiter so loud peaks don't distort) and a 0.3 second delay. With `--lan` it needs the write token. Use headphones if a mic is open nearby.

**Pack configurer** (click a pack)
- **Keys**: a drawing of the pack, with keys 1 to 4 on the top corners and 5 and 6 as the slim side buttons. Drag a key onto another to swap, drag a target from the list onto a key, drag a key off the pack to clear it (or press **Clear key**), or drag it onto another open pack to copy it. Nothing is written until you press Apply to Pack. Keys can point at a partyline, an audio channel, or another beltpack (a direct call). **Reset to profile** is at the far end and asks first.
- **Audio**: gains, volumes, sidetone, limiters, echo cancellation, noise filter, with the same ranges as the Riedel web UI. **Mic profiles** save the headset, internal and line input gains, the sidetone, echo cancellation and the noise filter (not volumes or limiters). They are kept in `profiles.json` in your user folder, so every browser and a new port sees them.
- **Controls**: speaker mode, silent mode, Bluetooth, display and key brightness, screen timers, and a menu lock that stops the wearer changing anything on the pack.
- **Duplicate**: copy a pack's profile, keys and audio onto another pack through five steps (choose, review, acknowledge, type the pack number, apply), then it reads the result back.
- **Restore** tab: **Undo last change** puts back what the last key, audio or controls change on that pack replaced (kept in memory, so a restart clears it), and below it is every snapshot that has this pack, each with a roll-back of just that pack.
- **VIP**: the ☆ VIP button makes a pack's battery and weak-signal alerts critical. When an antenna fills up (10 of 10) while a VIP is not connected, you get a warning. This only watches: it cannot give a pack priority on an antenna, which needs Riedel's priority licence.
- **Rename**: click the pack's name in its window to rename it on the antenna (needs the admin password; 24 characters at most, 16 fit on the pack's screen, which is flagged).
- **Check in/out** tab: the pack's note, who it is signed out to, the headset it has out (type, number, since when, with Returned, Not returned and Edit) or a form to check one out by hand, and every headset that has gone out with it.
- **Trends** tab: the pack's battery and signal for the last 1 to 24 hours, with a hover readout, red ticks where it dropped off, blue ticks where it changed antenna, and a one-line bar showing which antenna it was on.
- **Microphone**: which one a connected pack is using, as the system reports it: a wired headset, a **Bluetooth** headset, the pack's own speaker, or an RSM. It shows in the pack's window, on its grid card and in the map's hover card, and changes are logged.
- **Last seen**: for an offline pack, the time and how long ago, counting up.
- **Identify** flashes a pack and turns itself off after a set time (default 10 s).

**Help**: the Help button in the toolbar (or the `?` key, or Help > Comms Check help in the desktop app) opens a searchable guide inside the app, written for the people running the show. It is the short version of [docs/using-the-dashboard.md](docs/using-the-dashboard.md) and lives in `web/src/help.ts`.

**AI assistant (MCP)**: Settings has a section that makes a write token and the `claude mcp add` command for the MCP server, which is built into the program (`CommsCheck --mcp`, no Python needed). An assistant can read the system by default, and can only make changes once you give it the token.

**Tools menu**
- **Pre-show check**: one list of what is wrong (offline packs, low batteries, weak coverage, offline antennas, hot or worn batteries, firmware (naming the odd packs and antennas), lopsided antennas, pack names too long for the pack screen, setup drift, registration left open).
- **Setup snapshots**: a library of every snapshot of the partylines, profiles and each pack's name, number, keys and mic levels, newest first, with the date and time and how long ago. Save one with a name, let the dashboard save one by itself (at most every 10 minutes, only when the setup has changed), import or export a file, rename one, or make one the baseline that drift is measured against. **Restore** any snapshot: it shows a dry run, asks you to type `RESTORE`, and saves a "before restore" snapshot first so the restore can itself be undone. Automatic snapshots are capped at 300 (the oldest go); saved, imported and before-restore ones are never removed. The dashboard warns when the live system stops matching the baseline, for example after a config resync wipes the partylines. For audio, only the mic input levels count, because a wearer's own volume is not drift.
- **Audio channels**: the gain and mute of each audio input and output on the system (the gain the Riedel page sets on an audio channel, for example the Program input), with a slider, an Apply that asks first, and the channel's own limits. A beltpack's own microphone gain is in the pack's Audio tab.
- **Comms matrix**: a live grid of who can talk to or hear what (packs down the side, partylines, direct calls to other packs and audio channels across the top, with the key on the pack in each cell). Filter it, hide lines nobody uses, and export it as a landscape PDF or a standalone page.
- **Profiles**: every profile with its six keys and how many packs use it. Change a key, rename it, make a copy, or pick its **zone colour** from a row of swatches (kept between cyan and pink, because red, amber and green mean status). A pack's window has a profile box in its header to move the pack to another profile (the antenna resets the pack's keys and audio, so it asks, saves a snapshot first, and is blocked in show mode).
- **Partylines**: every partyline with how many the licence allows (12, or 32 with Extended Partylines). Create, rename, enable or disable, turn Show on Reply on or off (calls on that line then appear on the Reply key of the packs being called), and delete a line (type its name to confirm). Open a line to see which packs have a key on it, take it off a pack, or add a pack.
- **My pack and lines**: choose the comms tech's own beltpack once, then put any line on it, or take it off, in one click. "Enable alerts on this pack" makes that pack flash when the system raises an alert.
- **Battery swap list**: connected packs below the low-battery level, lowest first. A pack leaves the list by itself once its battery is changed.
- **Antenna log**: per antenna, a graph of pack count and a graph of temperature over the last 1 to 72 hours. An antenna warns when it gets hot (70°C by default, a setting). Each antenna is followed by its node id, so a rename keeps one history, and its card shows its MAC address.
- **Reports** and **Export snapshot (JSON)** are at the bottom of this menu.
- **Report** and **Post-show report**: minutes at low coverage, roams and drops per pack and per antenna, lowest battery, estimated battery life left. The post-show report is a one-page file with a preview, to download or print. Export CSV from the first.
- **Headsets: check in / out**: every headset that has gone out, by type, with Returned and Not returned buttons, a search box, and a button that copies what is still out.
- **Who has which pack**: a shared board filled in from the pack names, with a printable sheet.
- **Phone view**: a read-only page at `/m` that shows only problems, with a box to find a pack.

**Settings** (top bar): the low-battery level, alert sound, Identify timer, hot-antenna level, **new pack registration** (open or close wireless and NFC), the audio meter defaults, whether Unlatch all leaves VIP packs alone in show mode, and **keys not to unlatch** (for example key 6 if the program feed is always on key 6). **Antenna names** can be changed here (the name changes on the antenna itself, so it shows on its screen and in every list; an antenna that isn't answering can't be renamed). The antenna connection is changed from here too. Settings are saved and survive a restart.

**Show mode** (top bar): while it is on, risky changes are blocked (duplicate, restore, reset to profile, deleting a partyline) and every change that could disturb the comms asks first, with a plain description of what will change. The server enforces it. Turning it on warns if a pack is below the low-battery level (one row per pack) and saves a snapshot called "Show" by itself.

**Check in/out** (top bar): headset handling for the people handing packs out. With the switch on, a pack that turns on is asked "Did they collect a headset?": pick Single cup, Dual cup, Surveillance, Heavy or Lightweight and type or scan the headset's number (a barcode scanner that types like a keyboard works; Enter confirms). A pack that stays off for a minute with a headset out is asked "Was the headset returned?", showing the type and number, and a scan checks it is the right one. A short dropout is not a turn-off, and a burst of packs vanishing together (an antenna fault) is ignored. Each answer goes in the event log.

**Unlatch all** (top bar, with the mode switches): one click normally. In show mode you hold it for 3 seconds. With keys not to unlatch set, each other latching key is set to Momentary for about 4 seconds and put back, because the antenna's own unlatch lets go of everything. It remembers what to put back if the server restarts halfway.

Limits of the Bolero API, so you know what the page can't show:
- Signal is a 4-step level (Excellent, Good, Fair, Poor), shown as 4 bars like the Riedel page. There is no finer figure: the dBm monitoring feed needs Riedel's Advanced Monitoring licence, which the tested system doesn't have (turning the radio scanner on didn't help, and it dropped four packs).
- No live audio level for a beltpack, no talk activity, and no latch state: the antenna doesn't report them. The dashboard knows which keys are set to Latching, not which are held.
- A pack in a charger is powered off, so it just looks offline.
- Locate is a toggle that can't be read back. Press a key on a pack to stop a flash the dashboard doesn't know about.
- Registration is a system setting, not per pack. Removing a pack's registration is not offered here.

More detail: [docs/](docs/README.md) has a guide to using the dashboard, where the colours come from, and how it works inside.

## Running the plain server

Run the server program (`CommsCheck`, or `CommsCheck.exe` on Windows) from the unzipped folder. The desktop app does the same thing inside its window.

Open http://127.0.0.1:8080. The first time, the page asks you to connect: type the IP address of any one antenna, or press **Find antennas on this network** and pick one. The other antennas are found from it. The address, and the other antennas found, are remembered for next time.

The admin password is only needed to change things on the antennas. Enter it on the same screen, or later from the padlock at the top of the page. Leave it empty to just watch. The password is kept in the program's memory only, never in a file, so you enter it again after a restart. Notes, VIP marks, colours and check-in only change the dashboard's own data and need no password.

If you prefer the terminal, `BOLERO_HOSTS=192.168.1.20` and `BOLERO_ADMIN_PASSWORD` still work (use `read -rs BOLERO_ADMIN_PASSWORD && export BOLERO_ADMIN_PASSWORD` so the password stays out of your shell history). Antenna addresses must be on a private network (10.x, 172.16-31.x, 192.168.x or 169.254.x). The dashboard refuses anything else.

### Sharing it on the show network

```sh
export BOLERO_WRITE_TOKEN='something-long'
CommsCheck --lan            # all interfaces
CommsCheck --bind 192.168.1.50  # or one address (BOLERO_BIND does the same)
```

Listening beyond this computer means connecting, the password and writes also need the token (the page asks for it). Without `BOLERO_WRITE_TOKEN` it is read-only. **Reads have no login**: anyone who can reach the port can see the dashboard and the phone page. Only use this on a closed production network. `PORT` changes the port (default 8080).

### Phone alerts (optional)

Set up from the page: **Tools > Phone alerts (Telegram)**. It walks you through making a Telegram bot with @BotFather (use one made just for this), finding your chat, and sending a test message. Nothing is sent until you tick "Send alerts to my phone", and there is a switch for it in the top bar for when you walk away.

The bot token is checked with Telegram, never shown on the page again, and kept in a private file in the data folder only if you leave "Remember on this computer" ticked. You can send warnings and critical alerts, or critical only, and the same alert for the same pack goes out at most once every 5 minutes.

Tick "Answer status questions in this chat" and the bot replies to `status`, `offline`, `battery`, `antennas` and `vip`. Answers come as a short summary with aligned lists (long lists are cut, with a count of the rest). Questions are read-only, and only the chosen chat is answered (in a group chat, everyone in the group can ask). `unlatch` is the one command that changes anything: the bot asks for a YES within 30 seconds, and only does it in a private chat with the bot, never in a group. It needs the admin password, like the button.

Environment variables still work: `BOLERO_TELEGRAM_TOKEN` with `BOLERO_TELEGRAM_CHAT`, or `BOLERO_WEBHOOK_URL` for any webhook (it gets `{"text": "..."}`).

### Audio meter

Found automatically. To set it by hand: `BOLERO_METER_STREAM=<group>:<port>:<channels>` (for example `239.69.1.1:5004:2`). The machine running the dashboard has to receive that multicast.

### Saved data

Everything the dashboard keeps (log, snapshots, settings, notes, headset records and so on) is in the `data/` folder beside the program, or `BOLERO_DATA_DIR`. The desktop app keeps it in its own application data folder. Mic profiles and saved layouts are the exception: they are in `profiles.json` in your user folder (`BOLERO_DATA_DIR` if set, otherwise `~/Library/Application Support/Comms Check` on a Mac, `%APPDATA%\Comms Check` on Windows, `~/.config/comms-check` on Linux), never in the project. The data folder is git-ignored, and none of it contains the admin password, except `admin.bin` in the desktop app's data folder if you chose to keep the password in the keychain (it is encrypted).

## How it talks to the Bolero

The server polls the same REST API the Bolero web UI uses, at `http://<antenna>/rest/`, every 2 seconds and pushes changes to browsers over Server-Sent Events. Reads use the built-in `user:userPass` account the Riedel web UI itself uses for reads. Writes use HTTP Basic auth as `admin`.

| Dashboard route | What it does |
|---|---|
| `GET /api/state`, `/api/stream` | current state, and the SSE stream of it |
| `GET /api/log`, `/api/me`, `/api/meter` | event log, what writes are allowed, audio levels (SSE) |
| `GET /api/listen?id=&ch=` | one metered channel as 16-bit mono audio |
| `GET /api/preflight`, `/api/report?hours=`, `/api/antenna-history?hours=`, `/api/snapshot`, `/api/snapshots`, `/api/snapshot/export?id=`, `/api/checkout`, `/api/undo` | the Tools data |
| `GET /m` | the phone page |
| `POST /api/connect`, `/api/disconnect`, `/api/scan` | connect to an antenna and set or forget the admin password, find antennas on this network |
| `POST /api/my-pack`, `/api/my-pack/line` | choose our beltpack, add or remove a line on it |
| `POST /api/show-mode` | `{on: true\|false}` switch show mode (turning it off needs a confirmation) |
| `POST /api/settings` | the numbers in Settings |
| `POST /api/bp/<id>/locate`, `/unlatch`, `/revert-profile`, `/undo` | Identify on or off, release latched keys, re-apply the pack's profile, undo the last change |
| `POST /api/bp/<id>/keys`, `/audio`, `/controls`, `/rights`, `/duplicate`, `/line` | validated key, audio, controls, menu lock, duplicate and line writes |
| `POST /api/partyline` | `{action: create \| rename \| enable \| reply \| remove, ...}` (remove needs `confirm` = the line's name) |
| `POST /api/unlatch-all` | body `{"confirm": true}` |
| `POST /api/antenna/<nodeId>/locate/on\|off`, `/rename` | flash an antenna, rename it |
| `POST /api/registration` | `{ota?, nfc?}` open or close registration |
| `POST /api/snapshot` | `{action: "save" \| "restore" \| "use" \| "rename" \| "import", ...}` (a real restore needs `confirm: "RESTORE"`; restore, use and rename take an `id`) |
| `POST /api/vip`, `/api/pack-note`, `/api/profile-color` | the dashboard's own data: VIP marks, notes (a note only), profile colours |
| `GET`/`POST /api/stored` | saved mic profiles and layouts (`profiles.json` in the user folder) |
| `POST /api/headset`, `/api/check-mode` | headset check out, return, not returned, edit, skip; the check in/out switch |
| `GET /api/pack-trends?id=&hours=` | one pack's battery, signal and antenna samples, with its drops and moves |
| `POST /api/bp/<id>/name` | rename a pack (needs the admin password) |
| `POST /api/bp/<id>/profile`, `POST /api/profile` | move a pack to another profile; change a profile's keys, rename it, or copy it |
| `GET`/`POST /api/alert-packs`, `POST /api/alert-packs/test` | which pack flashes on an alert, and a test flash |
| `GET`/`POST /api/telegram`, `/api/telegram/find-chat`, `/api/telegram/remove`, `POST /api/notify-test` | set up and test phone alerts (the token is never returned) |
| `POST /api/checkout` | the pack board |

Only these routes exist. There is no pass-through to the antenna. Every body is checked against known fields and ranges, and unknown fields are refused.

`<id>` is the beltpack's internal id from `/rest/bp`, usually its number minus one. The page handles this. Which antenna a pack is on comes from `/rest/audioPorts`; `nodeStatus` alone only says which antenna reports it (the DECT master lists everyone).

## Legal

Comms Check is an independent project with no connection to Riedel Communications GmbH & Co. KG. "Riedel" and "Bolero" are trademarks of that company and are used only to say which equipment this works with. It uses an interface Riedel does not document or support, can change the settings of a live system, and comes with no warranty. Read [NOTICE.md](NOTICE.md) before using it at an event.

## License

MIT, see [LICENSE](LICENSE).
