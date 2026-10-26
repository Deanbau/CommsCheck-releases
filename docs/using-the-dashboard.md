# Using Comms Check

## Connecting

On first start the page asks for an antenna. Type the IP address of any one antenna, or press **Find antennas on this network** (it takes about 10 seconds, and works across the networks your computer is on) and click one in the list. Press Connect. The dashboard remembers the address.

The admin password is optional. Without it the dashboard only watches ("View only" in the top bar, with a "View only, enter password" button in the toolbar). With it, you can change things. Click the padlock at the top, or the toolbar button, to add the password or switch antenna, and "Turn control off" to forget it. In a browser the password is kept in memory only, so you type it again after a restart. The desktop app can keep it encrypted in the system keychain (see Desktop app below).

## The screen

**Top bar.** On the left: the Live chip (it pulses while data is arriving, and turns red when the data goes stale or the page loses its link), the connected count with a small bar, how many antennas are online out of the total, and an RF word. The RF word is Healthy, Watch (some fair signal), Degraded (a pack with poor signal, or an antenna off) or No packs. When new packs can register, a chip reads "Registration open: wireless and NFC" and opens Settings.

Then a group of switches: **Show mode**, **Check in/out** (headset check in/out), **Phone alerts**, and **Unlatch all**. After that come the audio meters, the padlock (green when control is on, red when it is view only), and **Settings**.

- **Audio meters** show one small meter (it turns red as soon as the watched channel is below the silence level, and pulses and reads SILENT once the silence alert has fired) per audio stream you have ticked, with a headphone button to listen to it. Click the meter for every stream: tick "Alert on silence" to switch a stream's silence alert on or off, and use the headphone beside a channel to listen to that channel.
- **Settings** (top right) holds the rest, in sections. Under Alerts: the **Low battery alert** percentage, the alert sound, and the Phone alerts set-up. Under Packs: the rules for Unlatch all (VIP packs, and keys that stay latched), and **Identify turns off after**, which is how many seconds a flashing pack keeps flashing before the dashboard turns it off (0 leaves it on). Then the hot antenna alert, **New pack registration** (wireless and NFC switches; in show mode, opening one asks first), audio meter defaults, antenna names, and the antenna connection.

**Toolbar.** Find a pack (press `/` to reach it), filter by zone (a zone is a Riedel profile), and the problem chips: Low battery, Weak signal and Offline, each with a count. Pick several to see any of them, and "Show all" clears them. Then Map and Grid. In grid view only, the toolbar adds Edit layout, Arrange (by pack number or by zone), Layout presets, Save layout, Delete preset, and hide offline packs (the hidden ones come back with "Show hidden"). Tools holds the rest. Fit is in the zoom control at the bottom right.

**Map.** Each antenna has a coverage circle with dotted rings. Closer to the antenna means a stronger signal. The rings show relative signal, not distance, and they have no labels. A pack sits on the ring that matches its signal, so a pack drifting outwards is losing signal. The slow sweep is just a status light: it runs while an antenna is online. Packs move to a new antenna with a short glide when they roam.

Each pack is a tile with its number, a battery bar under the number, and its name. A healthy pack is a plain, dimmer tile with no status text. The coloured dot on a corner is the zone. An amber border means the pack needs a look (a low battery, or fair or poor signal). A red border means a problem (battery under 15%, poor signal, or a VIP pack with a problem). The legend at the bottom explains the borders.

Hover a pack to enlarge it. Its card shows the battery and charge, the signal and antenna, the time left if the dashboard has an estimate, and whether it is offline and when it was last seen. Click a pack to open it. Shift-click (or cmd or ctrl-click) selects it instead.

Antennas are laid out by the dashboard, in rows of equal-size circles. There are no saved antenna positions to tidy up. Hover an antenna for its card: how many packs it has against its limit (amber from 80% full, red when full), its temperature (against the hot antenna alert), its address and firmware, and whether wireless or NFC registration is open. An antenna that stops answering stays on the map as an offline circle until it answers again. Click an antenna to open its window (Identify, rename, the packs on it, and its pack count and temperature over time). That needs the admin password.

While registration is open, every online antenna shows amber waves, an amber wifi mark, and a "Wireless open" or "NFC open" chip.

**Fit.** The Fit button in the zoom control (bottom right), or `f`, fits everything in the window. In fit mode there are no scrollbars, and the map follows new packs and antennas within a second or two. Zoom by hand to scroll.

**Grid.** One card per pack: number, name, profile, antenna, battery and signal bars, health word, and an ID button to flash it. Cards use the same colour rule as the map. The top edge is the zone colour, and only a problem turns the card amber or red. The battery bar is neutral unless the pack has a problem.

**Event log** (bottom). Newest at the bottom. Filter by type (the "All types" dropdown) or by pack, search, export CSV or JSON. Click a line to highlight its pack on the map.

## Show mode

The **Show mode** switch in the top bar is for when the show is live. While it is on (amber, with a line under the top bar):

- **Blocked:** duplicating a pack, restoring a setup snapshot, and resetting a pack to its profile. Unlatch all is not blocked: in show mode you hold the button for 3 seconds. Each is greyed out with the reason. The server refuses them too, so another browser tab or a script can't slip past.
- **Asks first:** any change that could disturb the comms. A box says exactly what will change ("Change key 3 on #8 Comms - Dean. This changes what that person can talk to and hear."), and nothing happens until you press **Make the change**. That covers key changes, audio and controls changes, the menu lock, unlatching one or several packs, and opening wireless or NFC registration.
- **Free:** looking, Identify and flashing, layouts, alerts, the pack board, and saving a snapshot.

Turning show mode on first lists any pack under the low-battery level, one row per pack, and asks whether to go ahead. It then saves a snapshot named "Show" with no question, so later changes are measured against it. Turning show mode off asks you to confirm. It stays on after a restart, and every open page follows it at once. It sits on top of the admin password: with show mode on, even someone who has the password gets the same blocks and questions.

## Headset check in/out

With the **Check in/out** switch in the top bar on, the dashboard keeps track of which headset went out with which pack.

- **A pack turns on** after being off for 2 minutes or more (or it was off when the dashboard first saw it). It is asked "Did they collect a headset?" The question has the headset types (Single cup, Dual cup, Surveillance, Heavy and Lightweight) and a headset number box. Scan the barcode or type the number, then press Enter. The last type you picked is chosen for you. "Not now" asks again in 10 minutes.
- **A pack turns off** for a minute or more with a headset out. It is asked "Was the headset returned?" Scan the headset to check it is the right one (optional), then press Returned or Not returned. "Not now" asks again later.
- **Several packs vanish at once** (more than three in one poll). That is treated as an antenna fault, so nobody is asked.

The pack's window has a **Check in/out** tab with its headset, type, number and when it went out, its history, and the name it is signed out to. Tools > Headsets: check in / out lists everything that is out. Records are kept in `headsets.json`. The earlier end-of-gig check-in is gone.

## Opening a pack

Click a pack. Shift-click (or cmd or ctrl-click) selects several instead, for bulk Identify and Unlatch from the toolbar.

The header shows the pack number and name, its zone dot and profile, and "connected" in quiet grey, or the time it was last seen in amber if it is off. Badges appear when they apply: the time left on the battery, a firmware badge if the pack is on a different firmware from the rest, "differs from profile", "unsaved changes", and a Note chip (click it to edit the note in the Check in/out tab). The Identify button flashes the pack. To rename the pack, click its name. That needs the admin password, and names can be 24 characters. Sixteen fit on the pack's own screen, and the pre-show check flags longer names.

The tabs are Keys, Audio, Controls, Check in/out, Trends, Restore, and Duplicate.

A chip in the pack's header says which microphone a connected pack is using (Headset, Bluetooth, Speaker or RSM). The system reports it; a Bluetooth headset connecting or dropping is logged.

**Keys tab.** The drawing is the pack. The four side buttons are keys 1 to 4 and the screen shows keys 1 to 6.

- Drag a key onto another to swap them.
- Drag a name from the Targets list onto a key to assign it. A target dropped on an empty key becomes Talk - Always Listen, momentary.
- Drag a key off the pack to clear it.
- Drag a key onto another open pack's key to copy it.
- Click a key to edit its function, mode and target in the boxes underneath.
- Targets include partylines, audio channels and other beltpacks (a direct call).

Yellow outlines show what has changed. **Nothing is written until you press Apply to Pack.** Revert drops your changes. Reset to profile is at the far end of the row. It re-applies the pack's Riedel profile, which also resets its audio settings, and it asks first in the dashboard's own dialog.

A "differs from profile" badge means the pack's keys no longer match its profile. That is normal after you change one pack on purpose.

**Audio tab.** Gains, volumes, sidetone, limiters, echo cancellation and noise filter. Apply to Pack writes only the settings you moved. Save as profile stores the headset mic, internal mic and line input gains, the sidetone, echo cancellation and the noise filter as a named mic profile. The Mic profiles menu loads one into the sliders (still press Apply). Profiles are kept in `profiles.json`, not in the browser, so every browser sees the same ones. Saving and deleting use the dashboard's own dialog. The antenna has no live mic level for a beltpack, so there is no mic meter.

**Controls tab.** Everyday settings for the pack:

- **Speaker mode** plays the intercom through the pack's own speaker. **Silent mode** mutes its beeps and call tones.
- **Bluetooth**: off, connect to a headset, or connect to a phone or PC, and the Local or Public mode.
- **Display and key brightness**: Off, Low, Medium, High or Custom (Custom unlocks sliders for the screen, the keys and their dimmed levels), plus how long until the screen dims and turns off.
- **Notifications**: for an incoming call, an incoming call in silent mode, low battery and out of range, choose whether the pack lights up, vibrates, beeps or speaks. A direct call from another pack counts as an incoming call, so ticking Vibrate there makes it buzz when someone calls it.
- **Menu access**: *Full menu locked* clears every right on the pack, so the person wearing it can't change anything, volume included. Unlocking puts back the access it had before (the dashboard remembers it). *Allow the main menu* switches just that one right on or off.

Like the other tabs, nothing is written until you press Apply to Pack, except the two menu access boxes, which act as soon as you tick them. The antenna reports "vibration disabled" and "Bluetooth disabled" for a pack as read-only status, and the tab shows that when it applies. It is not a setting.

**Check in/out tab.** The headset the pack has out, its type and number, since when, and buttons to mark it Returned or Not returned (or edit its type or number). With none out, a form takes a type and number to check one out. Below that are the pack's note, the "Pack signed out to" name (from the Who has which pack board), and the last few headsets that went out with this pack.

**Trends tab.** Battery and signal over the last six hours by default (up to 24), with a readout that follows the mouse. Red ticks mark where the pack dropped off, and blue ticks mark where it moved to another antenna. A one-line antenna timeline sits under the charts.

**Restore tab.** Undo last change, and the list of saved snapshots that hold this pack, each with what a rollback to it would change. Rolling back needs the admin password.

**Duplicate tab.** Copies this pack's profile, keys and audio onto another pack. Five steps: choose the pack and what to copy, review the exact changes, acknowledge, type the target's pack number, then apply. The result is read back from the antenna and each part is ticked or crossed. A pack's name and number are never copied.

## Tools menu

- **Pre-show check** lists everything that needs attention before doors: offline packs, low batteries, weak coverage, offline antennas, hot or worn batteries, mismatched firmware, lopsided antennas, drift from the saved setup, and registration left open.
- **Setup snapshots** save the partylines, profiles and every pack's name, number, keys and audio. After that the dashboard warns you if the live system stops matching it. Restore shows a dry run of every step, asks you to type RESTORE, then applies it. Save a new snapshot after any change you want to keep. Turning on Show mode saves one automatically.
- **Audio channels** lists every audio input and output on the system with its gain (inside the channel's own limits, for example -12 to +12 dB for an input) and a mute button. Move the slider and press **Apply**: it asks first, because everyone who hears that channel hears the change at once. It needs the admin password, and show mode asks again. This is the system's channel gain; a beltpack's own microphone gain is in the pack's Audio tab.
- **Comms matrix** shows who can talk to or hear what, live: one row per pack, one column per partyline or audio channel. A cell says what the pack's key does there (T talk, TL talk and always listen, T/L talk and listen, L listen, M monitor, N notification), with a dot if the key latches and the key's number on the pack. Direct calls (a key that calls another pack) have their own group of columns, one per pack that is called. Hover a cell for the details. Search by pack, show only packs on air, and hide lines nobody uses. **Export PDF** saves a one-page landscape (A3) file in the desktop app, or opens the print window in a browser; **Download page** saves it as a standalone page.
- **Profiles** lists every profile (with a zone dot and the number of packs on it) and its six keys. Change a key and press **Apply to the profile** (it asks first, and packs on the profile may follow, so check one afterwards). **Rename** and **Make a copy** are there too, and so is the profile's **zone colour**, a row of swatches. Packs whose keys no longer match are outlined in amber. To move a pack to another profile, open it and use the profile box in its header: the antenna resets that pack's keys and audio, so it asks first, saves a snapshot, and is blocked in show mode.
- **Chargers** shows each charger's bays, what is in each and what it is doing, and the same drawing sits at the bottom right of the map. The system only reports bay contents with the PRO 1 licence (Charger Monitoring); without it every bay reads empty, and the panel says so.
- **Coverage and battery report** shows minutes at low coverage, roams, drops and lowest battery per pack and per antenna, plus the estimated battery life left for each pack. Each estimate says whether it is *typical* (the charge divided by what a lightly used pack draws, available straight away) or *measured* (from that pack's own drain over the last 5 minutes or more, so it follows how hard the pack is being used). Packs on USB power get no estimate. Export CSV.
- **Who has which pack** is a shared board. It fills in from the pack names ("Role - Person"). Print sheet makes a printable table.
- **Headsets: check in / out** lists every headset that is out, with its pack, type and number, and marks any not returned.
- **My pack and lines** sets which beltpack is the comms tech's own (a global setting, kept on the server, so every screen agrees). After that, every partyline is listed with **Add to my pack** or **Remove**. A line goes on the first free key (1 to 6) as Talk - Always Listen, momentary, and Remove clears that key. If there is no free key it says so. In show mode each change asks first.
- **Phone alerts (Telegram)** sets up messages to your phone. Make a bot with @BotFather, paste its token, press Find my chat (after pressing Start on the bot), choose warnings and critical or critical only, tick Send alerts, and use Send a test message. The token is kept in a private file in the data folder unless you untick Remember.
- **Enable alerts on this pack** (in My pack and lines) makes your pack flash when the system raises an alert. Choose warnings or critical only. It flashes for the Identify time and stops by itself, at most once a minute. "Flash it now to test" tries it. An offline pack can't flash.
- **Partylines**, **Post-show report**, **Battery swap list** and **Antenna log** open their own panels. Each antenna is followed by its node id, so a rename keeps one history, and its card shows its MAC address.
- **Phone view** gives the address of a read-only page that shows only problems.
- **About and legal** shows the notice: Comms Check is unofficial and not connected to Riedel, plus the risks of using it on a live system.
- **Reports** lists packs below the low-battery level, offline packs, packs with fair or poor signal, and packs on USB power. Click one to find it on the map.
- **Export snapshot (JSON)** saves the current status to a file.

## Keyboard shortcuts

| Key | What it does |
|---|---|
| `?` | open Help |
| `/` | focus Find a pack |
| `e` | edit layout on or off (grid view) |
| `f` | fit everything in the window |
| `l` | show or hide the event log |
| `Esc` | leave a text box, clear the selection and the search |

## What the alerts mean

| Log entry | Meaning |
|---|---|
| online / offline | a pack reconnected or dropped. Offline is critical (red) in show mode, or when the pack's battery was under 10% when it dropped, and a warning otherwise. Its alert closes itself 10 seconds after the pack is back |
| battery low / ok | battery crossed the low-battery setting (critical under 15%) |
| low coverage / coverage ok | signal stayed at Fair or worse for about 6 seconds, then recovered. A pack that drops off the system within that time is reported only as offline |
| roam | a pack moved to another antenna |
| battery hot, battery health | battery over 45 degrees C, or health under 80% |
| firmware | a pack, or an antenna, is on a different firmware from the rest |
| headset | a headset was checked out, returned or marked not returned, or check in/out was switched on or off |
| flapping | a pack dropped off or changed antenna 4 times in 10 minutes. It is reported once per 10 minutes (critical for a VIP) |
| antenna balance / balance ok | one antenna is at least 80% full while another is at most half full, or the packs are spread out again |
| config | a pack's name, number, profile, keys or audio changed |
| drift | the live setup no longer matches the saved snapshot |
| reg open / reg closed | wireless or NFC registration was switched |
| identify, unlatch | a command was sent from the dashboard (Identify also covers the alert-pack flashes) |
| audio silent / ok / lost | the watched audio channel went quiet, came back, or the stream disappeared |
| antenna | an antenna went offline or came back |
| link lost / ok | the dashboard lost or regained contact with the system |

Alerts appear as cards at the top centre of the screen. When audio or the connection to the antennas comes back, a short message says so, on screen and on your phone. **Critical** ones (a pack offline, an antenna down, silent audio, a lost connection) stay until you press the cross, or close themselves 10 seconds after the problem clears. **Warnings** fade after about 9 seconds, and short confirmations after 4. The same alert repeating shows as one card with a count. Several alerts at once stack like a deck, with the newest in front. Hover the stack to spread it out, and "Clear all N alerts" removes them. A card with a pack in it has an "Open the pack" link. The optional sound and desktop notification fire for the serious ones. With phone alerts set up (Tools > Phone alerts), warnings and critical events are also sent to Telegram, at most once per pack per kind every 5 minutes.

## Diagnosis

The dashboard watches for a few patterns that are easy to miss by eye. Each one is in the event log, and most are in the pre-show check too.

- **Flapping.** A pack that keeps dropping off or hopping between antennas (4 times in 10 minutes) is named in the log. Drops that happen in a burst across several packs are not counted, because those are an antenna fault.
- **Antenna balance.** When one antenna is at least 80% full and another is at most half full, the dashboard names the three packs with the weakest signal on the full antenna and suggests moving them nearer the light one. It is logged once when it starts and once when it clears. The Antenna log shows the same message while it lasts.
- **Firmware.** Packs or antennas on a different firmware from the rest are named. The pack's window shows a badge. Judging needs at least three packs on the same firmware.
- **Likely flat.** A pack that drops off with less than 10% battery left is reported as "likely flat", and the alert is critical.

## Alerting a pack

The antenna can flash a pack's status light and screen yellow (Identify), and the dashboard uses that for alerts. It can't make a pack vibrate or beep on demand: the API has no call for it, and Identify only flashes. What a pack does for a call, low battery or out of range is its notification pattern, and the Controls tab lets you add Vibrate to those. The dashboard can't place a call itself, so that only fires when someone presses a key. For a buzz in someone's pocket, send alerts to their phone with Telegram.

Identify is a toggle that nothing reports back. If someone presses a key on a flashing pack, it stops, and the dashboard doesn't know. The dashboard's own timer would then switch it back on for a moment. Keep the Identify time short (the default is 10 seconds) so a mistake costs little.

## Desktop app

The desktop app is one window. It starts the server itself, shows a splash screen while that happens, and shows the dashboard in the same window. The server panel (interface, port, token and console) docks at the bottom, from the Server menu. If the server crashes, the app starts it again after a short wait, unless it has crashed five times in a minute.

When the app opens view-only, it asks for the admin password once. Tick "Remember it in the keychain" to keep the password encrypted with the system keychain, so the next launch does not ask. The Server menu has **Forget the saved admin password** to delete it. A password is never saved any other way.

On a Mac, a critical alert bounces the dock icon while the app is in the background.

## Good habits on show day

1. Run the pre-show check.
2. Save a snapshot once everything is right.
3. Leave the dashboard open on a spare screen. If a pack's coverage dips, the log and the map both show it.
4. After any antenna joins, leaves or restarts, glance at the Setup snapshot panel: that is when the system can resync and undo your partylines.
