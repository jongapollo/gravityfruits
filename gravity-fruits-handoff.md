# Gravity Fruits — Developer Handoff

**Project:** Gravity Fruits  
**Studio:** Apollo Games  
**Type:** Browser-based HTML5 mini game (crash/combo)  
**Target:** Portrait mobile (primary) · Landscape mobile (rotation) · Desktop (scaled)  
**Canvas:** 910 × 1672 px (portrait) / 1672 × 910 px (landscape) — scaled to fit window via JS transform

---

## Game Description

Gravity Fruits is a crash/combo mini game where fruits fall from the top of the arena into columns. A multiplier climbs in real time and the player can cash out at any moment or risk it until the game crashes. A special Mystery Box symbol triggers random bonus outcomes.

### Core Loop

1. Player sets a bet via manual adjuster or slider
2. Tap **START** — fruits fall, multiplier climbs
3. Tap **CASH OUT** to bank `bet × current multiplier`, or ride it out
4. Game can crash at any moment, paying nothing
5. After result, button resets to **START** after a 1.2s beat — tap to start a new round

---

## Symbols

| # | Symbol | Asset File |
|---|--------|------------|
| 1 | Cherries | `fruitsSVG/01-cherries.svg` |
| 2 | Grapes | `fruitsSVG/02-grapes.svg` |
| 3 | Watermelon | `fruitsSVG/watermelon-remake.svg` |
| 4 | Lemon | `fruitsSVG/04-lemon.svg` |
| 5 | Orange | `fruitsSVG/05-orange.svg` |
| 6 | Plum | `fruitsSVG/06-plum.svg` |
| 7 | Mystery Box *(special — loot drop only)* | `fruitsSVG/07-mystery-box.svg` |

### Mystery Box Outcomes

| Outcome | Weight | Effect |
|---------|--------|--------|
| `mult` | 45% | Applies a random ×2–×20 multiplier to the current mult |
| `reveal3` | 30% | Reveals all fruit values for 3 seconds |
| `freeze` | 25% | Locks current multiplier as guaranteed minimum payout |

---

## UI Layout — Portrait

```
┌─────────────────────────────────────────┐
│  [⌂]  GRAVITY FRUITS            [ i ]  │  ← Title bar
├─────────────────────────────────────────┤
│  MULT 1.00×    [history chips]          │  ← HUD overlay (top of arena)
│ ┌───────────────────────────────────────┐│
│ │                                       ││
│ │           ARENA (fruits fall)         ││  ← Game arena (flex: 1)
│ │                                       ││
│ └───────────────────────────────────────┘│
├─────────────────────────────────────────┤
│  [ − ]        $ 10        [ + ]         │  ← Bet adjuster
│  ══════●══════════════════════          │  ← Bet slider
│  [       START / CASH OUT      ]        │  ← Single merged action button
│  BALANCE               $1,000          │  ← Credit row
├─────────────────────────────────────────┤
│  SEED abc123…   CLIENT player [↩] [ ☰] │  ← Provably fair row
└─────────────────────────────────────────┘
```

### Action Button — States

| Game state | Appearance | Click |
|---|---|---|
| `idle` | Purple gradient — **START** | Start round |
| `running` | Green gradient — **CASH OUT** (line 1) / `$value` in white (line 2) | Cash out |
| `cashed` / `crashed` | Stays green for 1.2s beat, then flips purple | Start new round |

### Portrait Button & Element Sizes

| Element | Size | Style |
|---------|------|-------|
| START / CASH OUT | Full-width, `height:160px`, `font-size:36px` | Pill `border-radius:9999px`; purple or green gradient |
| Adjuster (− / +) | 120 × 120 px circle, `font-size:72px` | `#1e0830` bg, 5px `--line` border |
| Home / Info | 74 × 74 px circle | `#1e0830` bg, 5px `--line` border |
| Replay / Menu | 74 × 74 px circle | Same style |

### Portrait Slider Spacing

`bet-slider-wrap` padding: `18px 8px 16px` — thumb is equidistant (~14px) between the bet-pill and the action button.

---

## UI Layout — Landscape

When the device rotates to landscape, the canvas switches to **1672 × 910 px**. Layout uses CSS Grid named areas — the same HTML, no elements added or removed.

```
┌──────────────────────────┬──────────────────────┐
│                          │  [⌂] GRAVITY FRUITS [i]│  ← Title bar
│   MULT 1.00×  [history]  │──────────────────────│
│ ┌────────────────────┐   │  [ − ]  $ 10  [ + ]  │
│ │                    │   │  ══════●══════════    │  ← Slider (centered between pill & button)
│ │   ARENA            │   │  [      START      ]  │  ← Squarish button (fills panel)
│ │  (fruits fall)     │   │  BALANCE    $1,000    │
│ └────────────────────┘   │──────────────────────│
│                          │  SEED  CLI  [↩] [☰]  │  ← Fair row
└──────────────────────────┴──────────────────────┘
     ~65% width                  540px fixed
```

### Landscape Grid Template

```
grid-template-columns: 1fr 540px
grid-template-rows:    auto 1fr auto

grid-template-areas:
  "arena  titlebar"
  "arena  controls"
  "arena  fairrow"
```

### Landscape Button Sizes

| Element | Size | Style |
|---------|------|-------|
| START / CASH OUT | Full-width, `height:auto; flex:1; max-height:240px`, `font-size:44px` | Squarish `border-radius:32px`; purple or green gradient |
| Adjuster (− / +) | 82 × 82 px circle, `font-size:52px` | Same style |
| Home / Info | 56 × 56 px circle | Same style |

### Landscape Slider Spacing

`bet-slider-wrap` padding: `60px 8px 16px` — large top padding pushes thumb down to visual midpoint between pill and button, compensating for the `flex:1` space consumed by the action-row.

---

## History Chips (Multiplier History)

Displayed in a row at the top of the arena next to the MULT pill.

| Chip type | Class | Color |
|-----------|-------|-------|
| Cashed round | `mh-cash` | Green — `#4ade80` |
| Crashed round | `mh-crash` | Red — `#f87171` |

All chips use their own outcome colour (not just the most recent one).

---

## Settings Overlay

Three iOS-style toggle switches (124 × 68 px pill, white thumb):
- **Haptic Feedback** — guards `navigator.vibrate()`
- **Reduce Motion** — disables arena-shake CSS animation on crash

Labels on LEFT, toggle on RIGHT. Toggle OFF: bg `#3a3a4a`, border `#55556a`. Toggle ON: bg `#22c55e`.

> Note: Sound toggle was removed (Sound is always on).

---

## Provably Fair Row

Displays server seed hash. **No manual seed rotation button** — seed is generated server-side only and cannot be triggered by the player. Hash is still shown for verification.

---

## Color Palette

### CSS Custom Properties

| Token | Hex | Usage |
|-------|-----|-------|
| `--line` | `#6d28a0` | All borders (5px interactive, 3px arena) |
| `--text` | `#ffffff` | Primary body text |
| `--label` | `#fff9cd` | Warm cream — all labels, pill text |
| `--gold` | `#f5c518` | MULT value, BALANCE value |
| `--accent` | `#a855f7` | Accent purple — START button, slider fill |
| `--accent2` | `#c084fc` | Lighter accent purple — COMBO pill value |

### Contextual Hardcoded Colors

| Element | Color |
|---------|-------|
| Body background | `#080010` |
| Canvas background | `radial-gradient(ellipse at top, #2e0a40 → #110619)` |
| Title bar gradient | `#28083c → #1a0528` |
| Controls panel gradient | `#1e0a2c → #130720` |
| Arena inner gradient | `#09132a → #070e20` |
| Circle button bg | `#1e0830` |
| Circle button hover | `#2e1244` |
| START button | `linear-gradient(135deg, #7c3aed, #a855f7)` |
| START hover | `linear-gradient(135deg, #8b5cf6, #c084fc)` |
| CASH OUT button | `linear-gradient(135deg, #15803d, #22c55e)` |
| CASH OUT hover | `linear-gradient(135deg, #16a34a, #4ade80)` |
| CASH OUT label text | `#052e12` |
| CASH OUT price text | `#ffffff` |
| Bet amount pill bg | `#000000` |
| Toggle OFF bg / border | `#3a3a4a` / `#55556a` |
| Toggle ON bg / border | `#22c55e` / `#1fa34c` |
| Toggle thumb | `#ffffff` |
| Fruit cell bg / border | `#0b1735aa` / `#243354` |
| Loot cell bg / border | `#1f2d55cc` / `#5a79ff55` |
| Hint — ok | text `#b7f7d5`, bg `rgba(16,185,129,.12)` |
| Hint — bad | text `#fecaca`, bg `rgba(239,68,68,.12)` |
| Hint — warn | text `#fde68a`, bg `rgba(245,158,11,.12)` |
| Cashout glow text | `#ff79c6` |
| Provably fair muted text | `#7a60a0` |
| History chip — cash | `#4ade80` on `rgba(34,197,94,.15)` |
| History chip — crash | `#f87171` on `rgba(239,68,68,.15)` |

---

## Typography

| Usage | Font | Weight |
|-------|------|--------|
| All UI text | Montserrat | 700 / 800 / 900 |
| Cashout celebration overlay | Pacifico | Regular |

- All labels and button text: `text-transform: uppercase` + letter-spacing
- Label color: `--label` (`#fff9cd`)
- Value color: `#ffffff` or `--gold` (`#f5c518`) where noted

---

## Shape Language

- Heavy border-radius everywhere
- Title bar / sheet overlays: `48px`
- Arena wrapper: `44px`
- Controls panel: `52px`
- Action button portrait: `9999px` (full pill)
- Action button landscape: `32px` (squarish)
- All other buttons, pills, inputs: `60px`
- All interactive borders: `5px solid --line`
- Arena wrapper border: `3px solid --line`

---

## Assets Included

```
index.html              — game (self-contained, open in browser)
fruitsSVG/              — source SVG files (embedded inline in game JS, not loaded at runtime)
  01-cherries.svg
  02-grapes.svg
  watermelon-remake.svg
  04-lemon.svg
  05-orange.svg
  06-plum.svg
  07-mystery-box.svg
uiSVG/
  home.svg
  info.svg
  close.svg
  replay.svg
  menu.svg
```

---

*Prepared by Apollo Games · May 2026*
