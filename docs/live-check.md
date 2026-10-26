# Live check plan

A plan for trying Comms Check on a real Bolero system, in about two hours. Everything built since v0.1.0 has only been tried against a simulator, and the simulator makes some things up. This session finds out which.

The order matters. **Phase 1 only reads.** Nothing is written to the system until Phase 2, and Phase 2 only touches one spare pack that nobody is wearing.

## Rules for the day

1. Do not type the admin password until Phase 2.
2. Every write in Phase 2 goes to the spare pack, or to a test partyline called `ZZ Test`. Nothing else.
3. Do not run a real setup **Restore** (a dry run is fine), and do not press **Unlatch all** on the live system.
4. Leave Show mode off while testing. It blocks several of the things being tested.
5. If something looks wrong, stop. Write down the time and what you saw. Do not retry a write to see if it works the second time. The console (Server panel, Cmd+Shift+S) lists every request the app sent.

## Tonight (10 minutes)

- [ ] Stop both simulators. The one started with `sudo` needs: `sudo pkill -f sim_antenna.py`
- [ ] Quit Comms Check, then move its data aside. Nothing is deleted, and it can be put back:
  `mv ~/Library/Application\ Support/comms-check-desktop/data ~/Library/Application\ Support/comms-check-desktop/data-sim-backup`
  The data folder holds simulator antennas, snapshots and profiles. Left in place, the simulator's antennas would show as offline circles next to the real ones, and the drift check would compare the real system to simulator snapshots.
- [ ] If **Server > Forget the saved admin password** is enabled in the app, press it.
- [ ] Charge the laptop. Check the installed build is the latest: `git log -1` in the project, then reinstall if needed.
- [ ] Bring: the laptop and charger, a network cable and adapter, the admin password (written down, not saved), one spare pack that nobody will wear plus a charged battery, one or two headsets, your phone (for Telegram), and this page.

## Phase 0: connect (10 minutes)

1. Join the show network. Open Comms Check and connect with **Find antennas on this network**, or type one antenna's address.
2. Pick **Just watch** when it asks for the admin password.
3. Check:
   - [ ] Only real antennas are on the map. No `127.0.0.1` addresses anywhere (hover each antenna).
   - [ ] The number of antennas matches the system.
   - [ ] "Antennas n/n" in the header is right, and "Live" is on.

If a simulator antenna appears, stop and fix that first (Settings has the connection; the saved list is in `connection.json`).

## Phase 1: read only (40 minutes)

Open the Riedel page on any antenna (its address in a browser, then Nodes and Beltpacks) next to the dashboard. For each row, do the step, write what you saw, and mark it OK or Not OK in the table at the bottom.

| # | Check | How | Expected |
|---|---|---|---|
| 1 | Names and addresses | Hover each antenna. Compare with the Riedel Nodes page. | Same names, same addresses, same firmware |
| 2 | Pack counts and the limit | Hover an antenna: "n of N". Compare with Riedel's count per antenna. | Counts match. N is the real limit (not 10 unless it is 10) |
| 3 | Signal scale | Pick three packs. Compare the dashboard's word with the bars on the pack's screen and on the Riedel page. | Four levels. Write down which pack bars match Excellent, Good, Fair and Poor, and whether "Unknown" ever shows |
| 4 | Battery and temperature | Compare two packs' battery % with their own screens, and an antenna's temperature with the Riedel page. | Close to equal |
| 5 | Firmware | Hover an antenna, open a pack (firmware badge appears only if odd). Run the **Pre-show check**. | Versions match the Riedel page. The firmware line is right |
| 6 | Registration | Compare the "Registration open" chip (and Settings) with the Riedel page's registration state. Do not change it. | Same state |
| 7 | Antenna list | In a browser go to `http://<antenna>/rest/nodeConfig` on each antenna (this only reads; if it asks for a login, use the read-only one the Riedel page itself uses, `user` and `userPass`). Count the entries. | Write down: does each antenna list every antenna, or only itself? |
| 8 | Polling load | Leave the dashboard open for 10 minutes. Keep the Riedel page open and click around. Watch the event log. | The Riedel page stays quick. No "no answer" blips. Note the Mac's CPU use for the app |
| 9 | Roams and Trends | Switch the spare pack on and walk it from one antenna's area to another. Open its **Trends** tab after 5 minutes. | A roam line in the log, a blue tick, and a second colour in the antenna bar |
| 10 | Dropouts | Switch the spare pack off for 20 seconds and on again. | Offline and online lines in the log, nothing more (no headset question) |
| 11 | Check in/out | Turn **Check in/out** on. Switch the spare pack off for 3 minutes, then on. | A "Did they collect a headset?" question. Check out a Heavy headset with a number. Switch the pack off and wait a minute: a "Was the headset returned?" question. Scan a wrong number: an error. Then the right number: done. The log has each step |
| 12 | Alerts that should not fire | Over the whole phase, note every alert (flapping, antenna balance, firmware, dropout). | None that are false. Write down any that fire, with the time |
| 13 | Antenna balance | Look at the real pack counts. | No warning unless one antenna is at least 80% full and another at most half full |
| 14 | Screens | Hover the rightmost pack and antenna. Check the map in fit mode. Look for a scrollbar. | Cards are not cut off, no scrollbar, no overlapping circles |
| 15 | Splash and prompts | Quit and reopen the app. | A splash while it starts, then the admin password question (choose Just watch) |
| 16 | Bluetooth headset | Connect a Bluetooth headset to a pack, then look at the pack's header chip and the grid card. | Confirmed on the real system: `usedMicrophone` is 3 for Bluetooth, and the chip and "BT" tag show it. Check it drops back when the headset disconnects, and that wired headsets read as Headset |

Also write down the pack counts on the real system (how many registered, how many on air) and how many partylines it has, with the licence limit. Phase 2 needs the last one.

## Phase 2: writes on the spare pack (45 minutes)

Do not start until Phase 1 is done and nothing odd came up.

1. Enter the admin password. Tick **Remember it in the keychain** only if this is your own Mac.
2. **Setup snapshots**: save one called `Before live check`. This only copies the setup into the dashboard. It is your way back.
3. Put the spare pack on the system and switch it on.

| # | Check | How | Expected |
|---|---|---|---|
| 1 | Identify a pack | **Identify** on the spare pack. | The pack flashes yellow, and stops after the set time |
| 2 | Flash an antenna | Click an antenna on the map, then press **Identify** in its window. | The map shows it flashing green at once, the real antenna's LED flashes, and it stops after 10 seconds |
| 3 | Rename a pack, change its number (and an antenna from its own window) | Click the spare pack's name, type `ZZ Test`, Enter. After 3 seconds look at the pack's screen and the Riedel page. Then rename it back. | The name changes on the pack and on the Riedel page, both ways |
| 4 | Keys and Undo | Change one key on the spare pack, **Apply to Pack**. Check the pack's screen. Then **Restore** tab, **Undo last change**. | The key changes, then goes back |
| 5 | Audio and Undo | Change the sidetone and a mic gain, **Apply**. Check the Riedel page. Undo. Save and reapply a mic profile. | The values change and revert. The profile is in `profiles.json` in the user folder |
| 6 | Duplicate wizard | Copy another pack's profile and keys onto the spare pack (review step, then apply). | The result is read back with ticks. The spare pack matches |
| 7 | Roll back one pack | **Restore** tab, roll the spare pack back to `Before live check`. | Its name, keys and mic levels return. Nothing else on the system changes |
| 8 | Partylines | If the system is below its partyline limit: create `ZZ Test`, rename it, disable it, enable it, delete it (type its name). | Each step shows in the Riedel Partylines page. It is gone at the end |
| 9 | Password in the keychain | If you ticked the box: quit and reopen the app. | No question, and the console says the saved password was applied |
| 10 | Phone alerts (optional) | Tools, Phone alerts (Telegram): make a bot, find your chat, send a test. Then send `status`. | The test and the reply arrive on your phone |
| 11 | Alert flash (optional) | Tools, My pack and lines: pick the spare pack, enable alerts, **Flash it now to test**. | The spare pack flashes |
| 12 | Profiles | Tools, Profiles: change one key on a spare profile (or make a copy with **Make a copy** and edit that). Check whether the packs on it follow. Also try Rename. In a pack's header, move the spare pack to another profile and back. | The key changes on the Riedel Profiles page. Write down whether packs on the profile follow the change and whether a pack moved to a profile gets that profile's keys and audio. Copy and Rename are guesses at the antenna's format, so expect they may fail |
| 14 | Audio channel gain | Tools, Audio channels. On a channel nobody is listening to (or with the feed muted at the desk): move its input gain one dB and Apply, check the Riedel page's Audio Channels view shows the same, then put it back. Try Mute and Unmute the same way. | The Riedel page agrees both ways, and nothing else changes. The write is the Riedel page's own `PUT audioChannel` (action change, currentGain and mute), checked only against a stand-in antenna |
| 16 | Rotaries | Pack window, Keys tab: on the spare pack set Rotary 1 to Main, Apply. Check the pack's rotary and the Riedel page. Put it back. Only then try **All packs** if you want that. | Riedel shows Main for that rotary and the pack's volume knob follows. The write sends only the changed rotary (`rotaries: [{id, rotarySetting: 128}]`), which is a guess at what the antenna accepts, checked only against a stand-in |
| 17 | Quick menu | Pack window, Quick menu tab: on the spare pack add one item, move it, Apply. Check the pack's own Quick Menu and the Riedel page. Remove it again and Apply. | The pack's menu and the Riedel page both show the change and the change back. The write is `quickMenuEntries: [ids]` as the Riedel page sends it, checked only against a stand-in |
| 18 | Audio filters | Pack window, Audio tab: on the spare pack pick a different headset microphone filter, Apply, check the Riedel page, put it back. Then Tools, Audio filters: copy a Riedel filter, drag a band, Create, check it on the Riedel page's Audio Filters, assign it to the spare pack, then unassign and Delete it. | The Riedel page shows the same filters and bands, and the curve drawn here matches its graph closely. The writes are `PUT audioFilters/<id>/config` (create, change, remove) and `bpConfig.micFilter` etc., as the page sends them, checked only against a stand-in. The curve assumes a 16 kHz sample rate |
| 15 | Registration (only if the system is idle) | Settings, open wireless registration for a few seconds, then close it. | The header chip and antenna waves appear while it is open and go when it is closed. The Riedel page agrees |

## Phase 3: put it all back (15 minutes)

- [ ] The spare pack has its original name, keys and audio (roll back from `Before live check` if in doubt).
- [ ] `ZZ Test` is gone from the partylines.
- [ ] Registration is closed.
- [ ] If this is not your own Mac, press **Server > Forget the saved admin password** and **Turn control off**.
- [ ] Export the event log (CSV) and take screenshots of anything that looked wrong.
- [ ] Fill in the results table.

## Results

| Check | OK? | What you saw |
|---|---|---|
| P0 only real antennas | | |
| P1-1 names and addresses | | |
| P1-2 pack counts and limit | | |
| P1-3 signal scale | | |
| P1-4 battery and temperature | | |
| P1-5 firmware | | |
| P1-6 registration state | | |
| P1-7 does every antenna list all antennas | | |
| P1-8 polling load | | |
| P1-9 roams and Trends | | |
| P1-10 dropouts | | |
| P1-11 check in/out | | |
| P1-12 false alerts | | |
| P1-13 antenna balance | | |
| P1-14 screens | | |
| P1-15 splash and prompts | | |
| P1-16 Bluetooth state | | |
| P2-1 Identify a pack | | |
| P2-2 flash an antenna | | |
| P2-3 rename a pack | | |
| P2-4 keys and Undo | | |
| P2-5 audio and Undo | | |
| P2-6 duplicate wizard | | |
| P2-7 roll back one pack | | |
| P2-8 partylines | | |
| P2-9 keychain | | |
| P2-10 phone alerts | | |
| P2-11 alert flash | | |
| P2-12 profiles (edit, copy, rename, move a pack) | | |
| P2-13 audio channel gain and mute | | |
| P2-14 registration | | |
| P2-15 rotaries (Main, all packs) | | |
| P2-16 quick menu | | |
| P2-17 audio filters | | |

## What to do with the results

- Anything marked Not OK becomes a fix. Send the row, the time and a screenshot.
- Once Phase 2 passes, take those items off the "Not confirmed on a live system yet" list in the [README](../README.md), and tag the next version.
- The things most likely to need a change are the signal scale (#3), where firmware is reported (#5), the registration fields (#6), the antenna list (#7), the pack limit (#2) and the polling load (#8), because those are where the simulator guessed.
