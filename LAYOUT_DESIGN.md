# Corne 42-Key Layout — Design Doc (macOS + Aerospace + Neovim)

> Handoff document. Import this into a coding-agent session when iterating:
> run the CLI inside the repo, `/import LAYOUT_DESIGN.md`, then ask for changes.
> All chord mechanics below were verified against the ZMK hold-tap documentation.

## User profile

- Corne 42 keys (full 3x6 outer columns used), ZMK firmware, QWERTY
- macOS; Neovim (modal editing, `jk` escape, no Cmd-based editor shortcuts)
- Coding-heavy: wants unshifted symbols, easy numbers
- 80+ WPM typing speed — HRM misfire protection is mandatory
- Aerospace tiling WM is the window manager of choice; its primary modifier is **Alt (Option)**:
  - `alt+1..9` — switch workspace
  - `alt+h/j/k/l` — focus window
  - `alt+shift+h/j/k/l` — move window
  - `alt+<letter>` — app launchers
- macOS chords in browsers/docs: `Cmd+Tab`, `` Cmd+` ``, `Cmd+1..9` (tabs), `Cmd+Opt+1..9` (Google Docs)
- Right hand is frequently on the mouse — Aerospace control should be one-hand (left) operable

## Hard-won design decisions (do not regress these)

1. **Mod order on home row: Ctrl-Shift-Alt-GUI** (pinky→index, mirrored on right).
   Cmd/Alt end up on the strongest fingers and are the most-chorded mods.
2. **Bilateral positional hold-tap** (`hold-trigger-key-positions` = opposite hand + thumbs)
   + `hold-trigger-on-release` + `require-prior-idle-ms 150` + `balanced` flavor,
   tapping-term 280, quick-tap 175. This is urob's "timeless HRM" recipe verbatim.
   - `balanced` (NOT tap-preferred): hold resolves on interrupting key press+release,
     retroactively applying the mod — chords are instant, no waiting out a timer.
   - `hold-trigger-on-release` defers only the *positional* check to release,
     which is what allows same-hand modifier stacking (press both, release, tap).
3. **Dedicated left Alt on the left outer column (pos 24).** Aerospace uses Alt
   constantly; a plain `&kp` bypasses all hold-tap logic (same-hand chords work)
   and survives on layers via `&trans`. This also makes `alt+hjkl` and
   `alt+shift+hjkl` fully left-handed (right hand stays on the mouse).
4. **All 10 numbers on the RIGHT hand** (NUM layer, left thumb). Cmd/Ctrl live on
   the left home row, so `Cmd+num`, `Cmd+Opt+num`, `Alt+num` are all cross-hand.
   Left home-row mods stay alive *on the NUM layer itself* via `&trans`.
5. **Nav arrows mirrored to the LEFT hand: FDSA** (f=left, d=down, s=up, a=right —
   same finger roles as hjkl, mirrored). Frees the right hand for numbers and
   keeps Aerospace navigation one-handed. Selection: RSHFT (pos 35) + left arrows.
6. **Tab on a thumb (pos 41).** `Cmd+Tab` requires the chord key to be in the HRM's
   trigger list — thumbs are triggers, the corner position 0 is not. Bonus: the
   dedicated RGUI thumb (pos 39) + TAB enables right-hand-only Cmd+Tab.
7. **Symbol layer (SYM) holds full unshifted symbols** — coding never needs
   layer+shift. Opened from either corner (`lyr 3 ESC` / `lyr 3 GRAVE`).
   Backtick sits on the right hand (pos 6) so `` Cmd+` `` is cross-hand throughout.
8. **Caps-word combo moved to corner keys 0+11** — it must NOT sit on Alt+Shift
   (that chord is Aerospace move-window). Old combo (24+35) would conflict.
9. **Layer-tap uses a custom `lyr` behavior (hold-preferred, term 180, quick-tap 150)**
   instead of stock `&lt` (tap-preferred, 200ms): stock layer-tap delays layer
   activation by the full tapping term — unacceptable for `alt+hjkl` speed.

## Chord execution reference

| Chord | Execution |
|---|---|
| `alt+num` (workspace) | dedicated LALT(24) hold + NUM thumb(36) + right-hand num. Alt: RALT(40)+36 also works |
| `alt+hjkl` (focus) | LALT(24) + NAV thumb(38) + FDSA — entirely left hand |
| `alt+shift+hjkl` (move) | LALT(24) + RSHFT(35) + NAV(38) + FDSA |
| `alt+letter` (launcher) | LALT(24) + letter — no layer needed (dedicated key) |
| `Cmd+Tab` / `` Cmd+` `` | LGUI-HRM(F,16) + TAB(41); `` Cmd+` `` = F + GRV-corner(11) + backtick(SYM pos 6) |
| `Cmd+num` / `Cmd+Opt+num` | F(16, alive on NUM via &trans) + NUM(36) + num. **Opt comes from a dedicated Alt (24 or 40) — NOT the D-HRM stack** (see below) |
| `Cmd+arrows` (line jump) | J-RGUI(19, alive on NAV via &trans) + NAV(38) + FDSA — cross-hand |
| `Alt+arrows` (word jump) | dedicated LALT(24) or K-RALT(20, &trans) + NAV + FDSA |
| Shift+arrows (select) | RSHFT(35) + NAV(38) + FDSA |
| `Ctrl+arrows` | NOT available on NAV (A/Ctrl is overridden by the a=RIGHT arrow). Unused under Aerospace; revisit only if needed |
| Numbers in Neovim (`12j`) | NUM(36) hold + right-hand digits — no toggle needed at ≤4 digits |

## Day-1 verification checklist (in order)

1. Build via the repo's GitHub Actions workflow; flash both halves.
2. Fast typing smoke test (80+ WPM bursts): zero misfired mods. If misfires:
   raise `require-prior-idle-ms` (rule of thumb: 10500 / relaxed WPM ≈ 130).
3. `Cmd+Tab`, `` Cmd+` `` (both directions of the app-instance loop).
4. `alt+1..9` workspace switching; `alt+hjkl` focus; `alt+shift+hjkl` move.
5. `alt+<letter>` launchers (incl. left-hand letters — dedicated Alt bypasses bilateral).
6. `Cmd+num` browser tabs; `Cmd+Opt+num` Google Docs (via dedicated Alt + F stack).
7. `Cmd+arrows` / `Alt+arrows` / `Shift+arrows` text navigation + selection.
8. Symbol layer: every symbol, both hands; `Cmd+[` / `Cmd+]` indentation chords.
9. Caps word: press both corner keys (0+11) — must NOT interfere with alt+shift chords.
10. BT profile switching on NUM layer bottom-left.

## Known open question

**`require-prior-idle-ms` vs. simultaneous mod+mod presses.** Pressing two home-row
mods at the *same moment* may force the second one to tap (idle rule). This is why
`Cmd+Opt+num` is designed to use a **dedicated Alt** instead of the D+F stack.
If you ever need two HRMs stacked (e.g. `Ctrl+Shift+arrows`), test it; fallbacks:
press the outer mod a few ms before the inner one, or drop `require-prior-idle-ms`
to 125, or split a second HRM behavior without the idle rule for Ctrl/Shift.

## Agent pitfalls — do NOT "fix" these (learned the hard way)

1. **Never switch the HRM flavor to `tap-preferred`** (even if asked to reduce
   misfires). With `tap-preferred`, a hold only fires after `tapping-term-ms`
   expires, so every chord (`Cmd+num`, `alt+hjkl`, `Cmd+Tab`) waits ~280ms or
   fails. `balanced` resolves the hold when the interrupting key is pressed AND
   released and retroactively applies the mod — chords are instant. Misfire
   tuning belongs in `require-prior-idle-ms` ONLY.
2. **`hold-trigger-on-release` defers ONLY the positional (same-hand) check** to
   the interrupting key's release — it does NOT delay the modifier. Cross-hand
   chords still resolve instantly via the `balanced` flavor. Do not remove it:
   removing it re-enables same-hand misfires AND kills deliberate two-mod stacks.
3. **Do not replace the custom `lyr` behavior with stock `&lt`.** Stock `&lt` is
   tap-preferred with a 200ms term: the layer only activates after a 200ms hold
   (or never, if you tap a layer key quickly — it falls through to the tap
   keycode). `alt+hjkl` would feel broken.
4. **Chord keys must live in the HRM's trigger list.** This is why Tab is on a
   thumb (pos 41) and not the corner (pos 0): thumbs are in `THUMBS`, the corner
   is not. Never move a chorded key to a same-hand non-trigger position.
5. **Do not put the caps-word combo on Alt+Shift or the two shift keys** — those
   are Aerospace chords. It lives on corner keys 0+11 for a reason.
6. **`require-prior-idle-ms` can force a tap on near-simultaneous mod+mod
   presses.** This is why `Cmd+Opt+num` uses a DEDICATED Alt + the F-HRM instead
   of stacking two HRMs. Do not "simplify" that path away.

## Tuning knobs (only touch after a week of use)

| Symptom | Knob |
|---|---|
| Misfires while typing fast | ↑ `require-prior-idle-ms` (+10–25) |
| Mods feel laggy / hard to trigger | ↓ `tapping-term-ms` (280 → 240) |
| False mod on same-hand rolls | ↑ `tapping-term-ms`, or stricter index-finger behavior |
| Accidental layer activation | `lyr` flavor → `balanced`, or ↑ term 180 → 220 |
| FDSA arrows never feel natural | Swap NAV to right-hand arrows (needs numbers moved — ask the agent to re-derive) |

## Position map

```
  0  1  2  3  4  5      6  7  8  9 10 11
 12 13 14 15 16 17     18 19 20 21 22 23
 24 25 26 27 28 29     30 31 32 33 34 35
          36 37 38     39 40 41
KEYS_L = 0-5, 12-17, 24-29 | KEYS_R = 6-11, 18-23, 30-35 | THUMBS = 36-41
```
