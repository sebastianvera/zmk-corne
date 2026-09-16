# Corne 42-Key Layout — Design Doc (macOS + Aerospace + Neovim)

> Handoff document. Import into a coding-agent session when iterating:
> run the CLI inside the repo, `/import LAYOUT_DESIGN.md`, then ask for changes.
> All chord mechanics verified against the ZMK hold-tap documentation and
> audited against Miryoku (the most battle-tested minimal layout), urob's
> reference ZMK config, markstos's layout, and Seniply (Steve P).

## User profile

- Corne 42 keys (full 3x6 outer columns used), ZMK firmware, QWERTY
- macOS; Neovim (modal editing, `jk` escape, no Cmd-based editor shortcuts)
- Terminal-heavy: readline chords (Ctrl+A/E/K/U/W/R/C/D/Z) must work
- Coding-heavy: wants unshifted symbols, easy numbers
- 80+ WPM typing speed — HRM misfire protection is mandatory
- Aerospace tiling WM; its primary modifier is **Alt (Option)**:
  `alt+1..9` workspaces, `alt+hjkl` focus, `alt+shift+hjkl` move, `alt+<letter>` launchers
- macOS chords in browsers/docs: `Cmd+Tab`, `` Cmd+` ``, `Cmd+1..9`, `Cmd+Opt+1..9`,
  `Cmd+Shift+3/4/5` screenshots, `Cmd+T/W/R/L/Q`, `Cmd+Space`, zoom `Cmd/Alt + -/+`
- Right hand is frequently on the mouse — Aerospace control must be left-hand operable

## Key coverage matrix (audited — nothing missing)

| Category | Location |
|---|---|
| Letters A–Z | Base |
| Digits 0–9 | NUM right side |
| All 30 shifted/unshifted symbols | SYM, all unshifted |
| **F1–F12** | NAV right side (6–10, 30–34) + corners (0, 11) |
| **Media** (vol±/mute/prev/next/play) | NAV left bottom (17, 25–29) |
| **Forward Delete** | NAV 23 (`Cmd+Del` = delete-to-line-start via 12 or J) |
| Home/End/PgUp/PgDn | NUM 11/23/33/34 |
| Arrows | NAV left hand (FDSA mirrored) |
| Enter/Tab/Space/Backspace/Esc | Thumbs + corners (plain keys) |
| Caps Lock | caps-word combo (corner keys 0+11) — strictly better |
| All 8 modifiers | Dedicated LGUI(12)/LALT(24)/RSHFT(35)/RALT(40) + HRMs both hands |

## Hard-won design decisions (do not regress these)

1. **Mod order on home row: Ctrl-Shift-Alt-GUI** (pinky→index, mirrored right).
2. **HRM behaviors = urob's "timeless" recipe verbatim**: `balanced` flavor,
   tapping-term 280, quick-tap 175, require-prior-idle 150, bilateral
   (`hold-trigger-key-positions` = opposite hand + thumbs), `hold-trigger-on-release`.
3. **Dedicated left column for the two macOS/Aerospace primary mods:**
   **LGUI at pos 12, LALT at pos 24** — plain `&kp` keys. This is the single
   most important robustness decision: every frequent chord (alt+num, alt+hjkl,
   cmd+tab, cmd+num, cmd+letter, cmd+shift+arrow, cmd+space, zoom) is executed
   with plain keys — no hold-tap timing, no post-typing cooldown, no bilateral
   restrictions. The HRM mods remain for everything else.
4. **All 10 numbers on the RIGHT hand** (NUM, left thumb 36); left home-row
   mods stay ALIVE on NUM via `&trans` so cmd/alt+number stay cross-hand.
5. **Nav arrows mirrored LEFT (FDSA)** — Aerospace focus one-handed, mouse-free.
6. **Tab (41) and Enter (39) are plain thumb keys.** Chord keys must be in HRM
   trigger lists (thumbs are, corners are not). Enter must never be a hold-tap
   (roll-into-next-word misfires at 80+ WPM).
7. **SYM layer (v2, bigram-optimized): 30 unshifted symbols from either corner
   (`lyr 3 ESC` / `lyr 3 GRAVE`).** Backtick stays right (pos 6) so `` Cmd+` ``
   is cross-hand. Arrangement rules, in priority order — do not casually
   re-derive; every slot was placed against code-bigram hand analysis:
   - Bracket pairs split across hands, openers L / closers R, by frequency:
     `()` index homes (16/19), `[]` middle homes (15/20), `{}` adjacent inner
     homes (17/18), `<>` inner bottom (29/30).
   - Math `-` `=` left (3/4), `>` right (30): `->` `=>` `>=` `+=` `!=` `==`
     all alternate hands.
   - Sigils `#` `$` `@` `%` right top (7–10): `:%s`, `#include <`, `#{`,
     `${`, `@[`, `#[derive]` all cross-hand.
   - `~` left (13), `/` right (32): `~/`, `/* */`, `</` cross-hand.
   - `"` right (21), `'` left (27), `_` left (28): `{"`, `":`, `";`, and Vim
     registers `"+` / `"_` stay cross-hand.
   - Accepted same-hand losses: `<=` `-=` `/>` `{'` (each less frequent than
     the sequences it would displace).
8. **Custom `lyr` layer-tap (hold-preferred, 180ms, quick-tap 150)** — stock `&lt`
   delays layer activation by its whole tapping term; `alt+hjkl` would feel broken.
9. **Caps-word combo on corner keys 0+11** — must NOT sit on Alt+Shift or the
   shift keys (those are Aerospace / selection chords).
10. **NAV doubles as the system layer** (F-keys/media/DEL) — deviation from
    Miryoku (dedicated FUN/MEDIA layers) forced by having no free thumb key.
11. **Ctrl+letter chords use the RIGHT-hand Ctrl (`;` pos 22)** — cross-hand to
    every letter, so readline (Ctrl+A/E/K/C/D...) works instantly. Left Ctrl(A)
    same-hand chords require the hold-past-term technique (see limitations).

## Chord execution reference

| Chord | Execution |
|---|---|
| `alt+num` workspace | LALT(24) + NUM(36) + digit — or RALT(40) variant |
| `alt+hjkl` / `alt+shift+hjkl` | entirely LEFT hand: LALT(24) [+ RSHFT(35)] + NAV(38) + FDSA |
| `alt+letter` launcher | LALT(24) + letter |
| `Cmd+Tab` / `` Cmd+` `` | LGUI(12) + TAB(41) / + GRV-corner(11) + backtick(SYM 6) |
| `Cmd+[` / `Cmd+]` (back / indent) | LGUI(12) + GRV-corner(11) + LBKT(15) or RBKT(20) |
| `cmd+enter` / `alt+enter` | LGUI(12) or F(16) + RET(39) / LALT(24) + RET(39) |
| `cmd+letter` (T/W/R/L/Q…) | LGUI(12) + letter — plain keys, instant, incl. left-hand letters |
| `cmd+num` / `cmd+opt+num` | LGUI(12) [+ LALT(24) or RALT(40)] + NUM(36) + digit — all plain |
| `cmd+shift+arrow` (select-to-line) | LGUI(12) + RSHFT(35) + NAV(38) + FDSA — all plain |
| `Cmd+Shift+3/4/5` screenshots | LGUI(12) + RSHFT(35) + digit — all plain |
| `cmd/alt + -/+` zoom | mod + NUM(36) + MINUS/PLUS (NUM 30/31) |
| Ctrl+letter (readline) | RCTRL(`;` 22) + letter — cross-hand, instant |
| Ctrl+Shift+T etc. | `;`(22) + RSHFT(35) + letter (stack resolves via balanced) |
| `Cmd+Space` (Spotlight) | LGUI(12) + SPACE(37) |
| `Cmd+arrows` / `Alt+arrows` | LGUI(12) or J-RGUI(19, &trans) + NAV(38) + FDSA |
| Shift+arrows (select) | RSHFT(35) + NAV(38) + FDSA |
| F-keys | NAV(38) + F1–F12; media = NAV + left bottom row |
| `Cmd+Del` (del to line start) | LGUI(12) + NAV(38) + DEL(23) |

## Comparison with battle-tested layouts

| Practice | Miryoku / urob / markstos | This layout |
|---|---|---|
| Full TKL key coverage incl. F-keys, media | Yes (Miryoku baseline claim) | Yes (after audit) |
| Enter on a plain/dual thumb key | Yes (Miryoku base thumb) | Yes (39, plain) |
| Dedicated mods for WM use | markstos keeps dedicated Alt | Yes: LGUI + LALT both dedicated |
| HRM tuning | urob timeless recipe | Identical values |
| Layers opposite-hand from access thumb | Yes (Miryoku) | NUM: yes (right hand). NAV: same-hand — deliberate deviation for one-handed Aerospace/mouse use |
| Dedicated FUN/MEDIA layers | Yes (Miryoku tertiary thumbs) | Merged into NAV (no free thumb) — deviation |
| Unshifted symbol layer | Miryoku uses shift+sym pairs; markstos unshifted | Unshifted, cross-hand pair-split (opener-L / closer-R) |
| Arrows on right (vi HJKL) | Miryoku default | Left FDSA — deliberate deviation (mouse) |

## Known limitations (accepted, documented — verify on day 1)

1. **HRM cooldown**: an HRM pressed within 150ms of typing resolves as tap.
   All frequent chords avoid HRMs (dedicated mods), so this only affects
   HRM-based convenience variants. Knob: `require-prior-idle-ms`.
2. **Same-hand instant mod+alpha is blocked** by bilateral rules (that's the
   misfire protection working). Escape hatch: hold the mod ~300ms (past
   tapping-term), then tap the letter. Needed only for exotic same-hand chords.
3. **Shift+click** (range select with right hand on mouse): left Shift is S-HRM
   only — hold S ~300ms then click, or use caps-word. No dedicated left Shift slot.
4. **Mouse emulation omitted** (Miryoku has it; ZMK needs an external module).
   Add later only if wanted.
5. **F11/F12 on NAV corners** is nonstandard — done to keep BT row and PgUp/PgDn.
   macOS rarely uses them.

## Agent pitfalls — do NOT "fix" these (learned the hard way)

1. **Never switch the HRM flavor to `tap-preferred`** (even to reduce misfires).
   Chords would wait ~280ms or fail. Misfire tuning belongs in
   `require-prior-idle-ms` ONLY.
2. **`hold-trigger-on-release` defers ONLY the positional (same-hand) check** —
   it does not delay the modifier. Cross-hand chords resolve instantly via
   `balanced`. Do not remove it.
3. **Do not replace the custom `lyr` behavior with stock `&lt`** (tap-preferred
   200ms delays every layer activation).
4. **Chord keys must live in HRM trigger positions** — that's why Tab is on a
   thumb. If you add a chorded key, check `KEYS_L/KEYS_R/THUMBS`.
5. **Caps-word combo stays on 0+11.** Never Alt+Shift / shift pairs (Aerospace).
6. **LGUI(12) and LALT(24) must stay plain `&kp`.** Do not convert them to
   hold-taps to "recover" their positions — you would re-introduce the
   post-typing cooldown on every primary chord.
7. **Enter(39) and Tab(41) stay plain thumb keys** — see decision 6.
8. **Positions 12–17 on NUM and 18–23 on NAV stay `&trans`** — they keep the
   mods alive for cmd/alt+number and cmd/ctrl+arrows. Do not "use the free
   slots" for other bindings without re-deriving the chord impact.
9. **SYM bracket pairs stay split across hands (v2).** Corner-held layers make
   same-hand rolls worse, and empty brackets are typed constantly without IDE
   auto-pairs. Do not re-pair `(){}[]` on one hand or finger.
10. **Chord-bearing SYM slots: GRAVE 6, LBKT 15 / RBKT 20, `"` 21, `!` 22,
    `_` 28.** Do not "tidy" them back toward the old shift-row arrangement.
11. **SYM pos 34 is intentionally `&none`.** Every filled slot must re-derive
    its bigram impact; do not refill it with leftovers.

## Day-1 verification checklist (in order)

1. Build via repo's GitHub Actions; flash both halves.
2. Fast-typing burst test: zero misfired mods. If any: ↑ require-prior-idle
   (rule of thumb: 10500 / relaxed-WPM ≈ 130 at 80 WPM).
3. `Cmd+Tab`, `` Cmd+` ``.
4. `alt+1..9`, `alt+hjkl`, `alt+shift+hjkl`, `alt+<letter>`.
5. `Cmd+num` browser tabs; `Cmd+Opt+num` Google Docs.
6. `alt+enter`, `cmd+enter`; `Cmd+plus/minus` zoom.
7. `Cmd+arrows`/`Alt+arrows`/`Shift+arrows`; `Cmd+Shift+arrows` selection.
8. Ctrl+letter readline: Ctrl+A/E/K/C/D/W/R — all via `;` + letter.
9. SYM layer: all 30 symbols both hands; `Cmd+[`/`Cmd+]` indent (use corner 11);
   bigram spot-checks: `()` `{}` `->` `=>` `:%s` `");` `~/` `/* */` `{"` `":`.
10. F-keys (NAV + right hand) in a TUI (htop); media keys.
11. `Cmd+Shift+4` screenshot; `Cmd+Space` Spotlight; `Cmd+Del`.
12. Caps word (corners 0+11) — must not fire during alt+shift chords.
13. BT profiles (NUM bottom-left); PgUp/PgDn/Home/End (NUM right).

## Tuning knobs (only after a week of use)

| Symptom | Knob |
|---|---|
| Misfires while typing | ↑ require-prior-idle (+10–25) |
| Mods hard to trigger / laggy | ↓ tapping-term 280 → 240 |
| Accidental layer activation | lyr flavor → balanced, or term 180 → 220 |
| FDSA arrows never feel natural | Swap NAV to right-hand arrows (requires re-deriving numbers — ask the agent) |

## Position map

```
  0  1  2  3  4  5      6  7  8  9 10 11
 12 13 14 15 16 17     18 19 20 21 22 23
 24 25 26 27 28 29     30 31 32 33 34 35
          36 37 38     39 40 41
KEYS_L = 0-5, 12-17, 24-29 | KEYS_R = 6-11, 18-23, 30-35 | THUMBS = 36-41
```
