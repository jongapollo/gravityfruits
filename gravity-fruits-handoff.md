# Gravity Fruits — Developer Handoff

**Project:** Gravity Fruits  
**Studio:** Apollo Games  
**Type:** Browser-based HTML5 mini game (crash/combo)  
**Target:** Portrait mobile (primary), desktop (scaled)  
**Canvas:** 910 × 1672 px fixed, scaled to fit window via JS transform

---

## Game Description

Gravity Fruits is a crash/combo mini game where fruits fall from the top of the arena into columns. A multiplier climbs in real time and the player can cash out at any moment or risk it until the game crashes. A special Mystery Box symbol triggers random bonus outcomes.

### Core Loop

1. Player sets a bet (preset buttons or manual adjuster)
2. Tap **START** — fruits begin falling, multiplier climbs
3. Tap **CASH OUT** to bank `bet × current multiplier`, or ride it out
4. Game can crash at any moment, paying nothing
5. **START** after a result auto-resets and starts a new round

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

## UI Components

### Layout (top → bottom)

1. **Title bar** — Info button (circle, left)
2. **Arena wrapper** — HUD strip (MULT / COMBO pills) + game arena
3. **Controls panel**
   - Preset row: ½ / 2× / MAX
   - Adjuster row: − / $amount / +
   - Action row: START (full-width) then CASH OUT (full-width, same size)
   - Credit row: BALANCE pill (full width)
4. **Provably fair row** — Rotate button (left) + SEED pill + CLIENT pill + Menu button (right)

### Button Sizes

| Button | Size | Style |
|--------|------|-------|
| START | Full-width, 36px font, 28px 72px padding | Purple gradient, no border |
| CASH OUT | Full-width, same as START | Green gradient, no border, dark green text |
| Preset (½ / 2× / MAX) | Ghost pill | Same ghost style |
| Adjuster (− / +) | 144px circle | 87px font |
| Info / Rotate / Menu | 74px circle | `#1e0830` bg, 5px `--line` border |

### Settings Overlay

Three iOS-style toggle switches (124 × 68 px pill, white thumb):
- **Sound** — guards all SFX (`beep()`)
- **Haptic Feedback** — guards `navigator.vibrate()`
- **Reduce Motion** — disables arena-shake CSS animation on crash

Labels on LEFT, toggle on RIGHT.

---

## Color Palette

### CSS Custom Properties

| Token | Hex | Usage |
|-------|-----|-------|
| `--bg` | `#110619` | Main canvas background |
| `--panel` | `#1c0a2a` | Panel / card surfaces |
| `--panel2` | `#14061e` | Secondary panel depth |
| `--line` | `#6d28a0` | All borders (5px interactive, 3px arena) |
| `--text` | `#ffffff` | Primary body text |
| `--label` | `#fff9cd` | Warm cream — all labels, pill text |
| `--muted` | `#9070b0` | Secondary / de-emphasised text |
| `--gold` | `#f5c518` | MULT value, BALANCE value |
| `--gold2` | `#ffe97a` | Lighter gold highlight |
| `--good` | `#22c55e` | Success / CASH OUT button base |
| `--bad` | `#ef4444` | Error state |
| `--warn` | `#f59e0b` | Warning state |
| `--accent` | `#a855f7` | Accent purple — START button, slider fill |
| `--accent2` | `#c084fc` | Lighter accent purple — COMBO pill value |

### Contextual Hardcoded Colors

| Element | Color |
|---------|-------|
| Body background | `#080010` |
| Canvas background | `radial-gradient(ellipse at top, #2e0a40 → #110619)` |
| Outer wrap gradient | `#28083c → #1a0528` |
| HUD strip gradient | `#200a30 → #180622` |
| Controls panel gradient | `#1e0a2c → #130720` |
| Arena inner gradient | `#09132a → #070e20` |
| Circle button bg | `#1e0830` |
| Circle button hover | `#2e1244` |
| START button | `linear-gradient(135deg, #7c3aed, #a855f7)` |
| START hover | `linear-gradient(135deg, #8b5cf6, #c084fc)` |
| CASH OUT button | `linear-gradient(135deg, #15803d, #22c55e)` |
| CASH OUT hover | `linear-gradient(135deg, #16a34a, #4ade80)` |
| CASH OUT text | `#052e12` |
| Bet amount pill bg | `#000000` |
| Toggle OFF bg / border | `#3a3a4a` / `#55556a` |
| Toggle ON bg | `#22c55e` |
| Toggle thumb | `#ffffff` |
| Fruit cell bg / border | `#0b1735aa` / `#243354` |
| Loot cell bg / border | `#1f2d55cc` / `#5a79ff55` |
| Hint — ok | text `#b7f7d5`, bg `rgba(16,185,129,.12)` |
| Hint — bad | text `#fecaca`, bg `rgba(239,68,68,.12)` |
| Hint — warn | text `#fde68a`, bg `rgba(245,158,11,.12)` |
| Cashout glow text | `#ff79c6` |
| Provably fair muted text | `#7a60a0` |
| Debug panel | bg `#0b1735f0`, border `#3956aa`, text `#cfe6ff` |

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
- All buttons, pills, inputs: `60px`
- All interactive borders: `5px solid --line`
- Arena wrapper border: `3px solid --line`

---

## Assets Included

```
index.html              — game (self-contained)
fruitsSVG/
  01-cherries.svg
  02-grapes.svg
  watermelon-remake.svg
  04-lemon.svg
  05-orange.svg
  06-plum.svg
  07-mystery-box.svg
uiSVG/
  info.svg
  close.svg
  replay.svg
  menu.svg
```

---

*Prepared by Apollo Games · May 2026*
