# Offline projects: plan

Program a show's packs, partylines and profiles at home, then put them on the real system when you get to the venue.

Nothing in this plan is built yet. This page is the plan, and it gets updated as pieces land.

## Decisions already made

| Question | Answer |
|---|---|
| Main use | Pre-building a new show before arriving (not rehearsing changes to a running one) |
| How offline packs match real packs | By pack number |
| What a project holds | Packs, partylines and profiles only. No antennas, NSAs, licences or network settings |

## What a project holds

- **Packs:** number, name, profile, keys 1 to 6, audio levels and filters, rotaries, quick menu, and the control settings the pack window already edits.
- **Partylines:** name, enabled, Show on Reply.
- **Profiles:** name, keys, and the same audio, rotary and quick menu settings.
- **Raw copies of the replies it was started from.** Fields the dashboard doesn't understand are kept, so a later write sends them back unchanged.

A project does not hold: antennas, NSAs, network and licence settings, firmware, registration, who is wearing which pack, or the audio filters themselves (a profile or pack names the filter it uses, and the check below says if the live system lacks it).

Packs in a project are slots. A slot is a number and a name with its settings, waiting for a real pack to take it.

## How it works

Everything the dashboard asks of an antenna goes through one function, `bolero()`. In offline mode that function reads from and writes to the project file instead. The existing screens (Grid, pack window, Partylines, Profiles, Comms matrix and its PDF) work unchanged, and nothing reaches an antenna.

- A banner says **Working offline: Project X**, and the admin password is not asked for.
- Slots show as grey "planned" packs. There is no battery, signal or antenna, because there is no hardware.
- Edits go through the same validators the live writes use, so a project can't hold something the antenna would refuse (key range, 16 quick menu items, 10 filter bands, partyline limit).
- Undo works inside the project.
- A project is one file. It can be copied, duplicated ("v2"), sent to another tech and opened on another computer.
- A project starts from **a capture of the live system**, from **a previous show's project**, or **blank**.

I'm not building a pretend antenna with its own web server. The old simulator guessed wrong in places (the signal scale, the pack limit), and anything we invent can be wrong the same way. Starting from real captured replies keeps the project honest about the shapes. The antenna's own rules (the partyline limit by licence, change rights, what a pack gets when it changes profile) still only show up against the real thing, which is why the apply step reads everything back.

## Transition to live: the setup wizard

A wizard, opened from the project (and from the connect screen when a project is open). Seven steps.

1. **Connect and add every antenna.** The existing "Find antennas on this network" flow, then a list of what it found with a tick for each. The wizard won't go on until every antenna the system reports is up. This is also the guard against the config-resync trap, where an antenna joining silently undoes changes.
2. **Check.** Read-only, shown as a list of ticks and warnings:
   - show mode is off
   - the project's partyline count fits the live limit (12, or 32 with the Extended licence)
   - every profile and filter the project names exists live (or can be created)
   - no two project slots share a number, and no two live packs do
   - the antennas have room for the packs (the per-antenna limit)
3. **Match packs.** A table with the project's slots on the left and the real registered packs on the right, paired by number. Each row is one of:
   - **Matched:** a real pack has the slot's number. Shown with the name each side has.
   - **Slot with no pack yet:** nothing registered under that number. The slot's changes are held and sent when a pack with that number appears (the same idea as the queued rename for an offline pack).
   - **Pack with no slot:** a real pack whose number isn't in the project. Choose: leave it alone, or add it to the project.
   - **Disagree:** a real pack came up as #17 but the plan says #5. Pair them by hand, and the apply step renumbers the real pack to #5 if #5 is free. This is where the duplicate-number tools from the live work come in.
4. **Review the changes.** The diff, grouped as Partylines, Profiles, then one block per pack, with a plain line for each (`Key 4: Veritas -> MainStage`, `Rotary 1: Key 1 -> Main`). Each line has a risk mark: adds are quiet, changes to a pack that is on air are flagged, removals are off by default. Nothing is deleted from the live system unless that line is ticked, one at a time.
5. **Save a way back.** The app saves a snapshot called "Before applying <project>", then asks for the admin password and for `APPLY` to be typed. The plan is recomputed from a fresh read right before this step, and if anything changed since step 4 (an antenna dropped, a pack renumbered) it shows the new diff instead of applying the old one.
6. **Apply.** One write at a time, in this order:
   1. create partylines, then read back their new ids
   2. create profiles (if any are missing), then read back their ids
   3. per pack: profile first (assigning a profile resets keys and audio), then keys, audio, rotaries, quick menu, name and number
   4. partyline renames, enable and Show on Reply last

   Every pack is read back after its writes and compared with the plan. A mismatch is marked, not hidden. The first refused write stops the run. The wizard then offers **retry the failed step**, **skip it**, or **roll back to the snapshot**. The run's progress is saved, so closing the app part way and reopening it resumes where it stopped.
7. **Report.** What was done, what was skipped, what failed, and what is waiting for a pack to register. It can be saved as a PDF next to the Comms matrix.

The wizard never touches antennas, NSAs, nets, licences, firmware or registration. It is blocked in show mode.

## Phases

| Phase | What | Size | Done when |
|---|---|---|---|
| 0 | Live-check Phase 2 passes for every write the apply uses: pack name and number, keys, audio, rotaries, quick menu, profile edit and copy, partyline create and rename | Your time on site | Those rows in `docs/live-check.md` are OK. The apply doesn't ship before this |
| 1 | Project file, capture from live, open, save, duplicate. Offline backend that serves reads. Banner. Read-only screens work | Small | A captured project opens and every screen shows its data with the antennas unplugged |
| 2 | Offline writes: pack, partyline and profile edits land in the project through the existing validators. Undo. Start blank or from a previous project. Matrix PDF from a project | Medium | A show can be built from nothing and printed without touching a real system |
| 3 | The compare engine: a pure module (like `snapshot.py`) that takes a project and the live system and returns an ordered plan with plain descriptions and risk marks | Medium | Golden-file tests against captured real replies, including new partylines that need the two-step id handling |
| 4 | The wizard: connect, check, match, review, snapshot and confirm, apply with read-back, report | Large | Applying a project to a stand-in antenna gives a read-back that matches, and a forced mid-run failure resumes and rolls back correctly |
| 5 | Rehearsal on the real system: spare pack and a test partyline first, then a whole small project. Help text, README, live-check rows | Your time on site | The live rows pass and the first real project goes on cleanly |

Phases 1 and 2 are useful on their own: a show can be prepared and printed before the apply exists.

## Safety rules

- Offline mode can't send anything. A project has no admin password and no antenna address.
- The apply never deletes by default, and never touches anything outside packs, partylines and profiles.
- It is blocked in show mode, and refuses if an antenna is down or the antenna count changed since the plan was made.
- Every real apply is preceded by a saved snapshot and ends with a read-back.
- Matching is by number only. A pack that isn't matched is never written to.
- Writes that are still guesses are marked in the review step until they have passed on the real system.

## Testing

- Captured real replies from your system, with names changed, become fixtures. Plans are tested against them, so a change that alters the plan for a known setup fails a test.
- The stand-in antenna used in the tests learns the real write semantics (create a partyline, get a new id back; assigning a profile resets keys), so the apply can be run end to end with no hardware.
- Failure runs: a write refused part way, an antenna dropping between review and apply, two real packs with one number, a slot with no pack, a pack with no slot.
- Then the real rehearsal in phase 5.

## Risks

- **Profile creation** (copy and rename) is a guess at the antenna's format and may not work. If it doesn't, projects can only use profiles that already exist live, and the check step says which are missing.
- **New pack numbers.** A pack registering on site takes the number the antenna gives it, not the slot's. That is what the Match step is for, but it means packs should be registered before the wizard runs, or their slots are held for later.
- **Resync.** The apply is only as safe as the system is quiet. It refuses when antennas are missing, and re-reads after finishing.
- **Models.** A mini or 2.4 GHz pack offers fewer options than a standard pack (rotary bits, quick menu items). A project slot needs a model so its validator matches the real pack. Slots start as a standard pack and take the real model once matched, with any setting the real model can't hold shown as a warning.

## Not in this plan

Antennas, NSAs, audio channels and their gains, network and licence settings, firmware, the audio filter editor's own filters, and live-sharing a project between two computers.
