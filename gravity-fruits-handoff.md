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

## UI Layout — Portrait

```
┌─────────────────────────────────────────┐
│  [⌂]  🍉 GRAVITY FRUITS   [↩]   [ i ]  │  ← Title bar
├─────────────────────────────────────────┤
│  MULT 1.00×    COMBO 0 (+0.00×)         │  ← HUD strip
│ ┌───────────────────────────────────────┐│
│ │                                       ││
│ │           ARENA (fruits fall)         ││  ← Game arena (flex: 1)
│ │                                       ││
│ └───────────────────────────────────────┘│
├─────────────────────────────────────────┤
│  [ − ]        $ 10        [ + ]         │  ← Bet adjuster
│  ══════●══════════════════════          │  ← Bet slider
│  [          START         ]             │  ← Primary action
│  [         CASH OUT       ]             │  ← Secondary action
│  BALANCE               $1,000          │  ← Credit row
├─────────────────────────────────────────┤
│  [↻]   SEED abc123…   CLIENT player [☰] │  ← Provably fair row
└─────────────────────────────────────────┘
```

### Portrait Button Sizes

| Element | Size | Style |
|---------|------|-------|
| START | Full-width, `font-size:36px`, `padding:28px 72px` | Purple gradient, no border |
| CASH OUT | Full-width, same as START | Green gradient, no border, text `#052e12` |
| Adjuster (− / +) | 120 × 120 px circle, `font-size:72px` | `#1e0830` bg, 5px `--line` border |
| Home / Replay / Info / Rotate / Menu | 74 × 74 px circle | `#1e0830` bg, 5px `--line` border |

---

## UI Layout — Landscape

When the device rotates to landscape, the canvas switches to **1672 × 910 px**. Layout uses CSS Grid named areas — the same HTML, no elements added or removed.

```
┌──────────────────────────┬──────────────────────┐
│                          │  [⌂] GRAVITY FRUITS [↩][i]│  ← Title bar
│   MULT 1.00×  COMBO 0    │──────────────────────│  ← HUD pills (right panel)
│ ┌────────────────────┐   │  [ − ]  $ 10  [ + ]  │
│ │                    │   │  ══════●══════════    │  ← Controls (flex: 1)
│ │   ARENA            │   │  [      START      ]  │
│ │  (fruits fall)     │   │  [    CASH OUT     ]  │
│ │                    │   │  BALANCE    $1,000    │
│ └────────────────────┘   │──────────────────────│
│                          │  [↻] SEED  CLI  [☰]  │  ← Fair row
└──────────────────────────┴──────────────────────┘
     ~65% width                  540px fixed
```

### Landscape Grid Template

```
grid-template-columns: 1fr 540px
grid-template-rows:    auto auto 1fr auto

grid-template-areas:
  "arena  titlebar"
  "arena  lshud"
  "arena  controls"
  "arena  fairrow"
```

The `.ls-hud` row is a duplicate of the portrait HUD pills — the original HUD strip inside the arena is hidden (`display:none`) in landscape. Both sets stay in sync via JS.

### Landscape Button Sizes

| Element | Size | Style |
|---------|------|-------|
| START | Full-width, `font-size:32px`, `padding:36px` | Same purple gradient |
| CASH OUT | Full-width, same as START | Same green gradient |
| Adjuster (− / +) | 82 × 82 px circle, `font-size:52px` | Same style |
| Home / Replay / Info / Rotate / Menu | 56 × 56 px circle | Same style |
| MULT / COMBO pills | `flex:1` each, fills row equally | `font-size:20px` |

---

## Settings Overlay

Three iOS-style toggle switches (124 × 68 px pill, white thumb):
- **Sound** — guards all SFX (`beep()`)
- **Haptic Feedback** — guards `navigator.vibrate()`
- **Reduce Motion** — disables arena-shake CSS animation on crash

Labels on LEFT, toggle on RIGHT. Toggle OFF: bg `#3a3a4a`, border `#55556a`. Toggle ON: bg `#22c55e`.

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
| HUD strip gradient | `#200a30 → #180622` |
| Info / Settings sheet | `#1e0a2c → #110618` |
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
| Toggle ON bg / border | `#22c55e` / `#1fa34c` |
| Toggle thumb | `#ffffff` |
| Fruit cell bg / border | `#0b1735aa` / `#243354` |
| Loot cell bg / border | `#1f2d55cc` / `#5a79ff55` |
| Hint — ok | text `#b7f7d5`, bg `rgba(16,185,129,.12)` |
| Hint — bad | text `#fecaca`, bg `rgba(239,68,68,.12)` |
| Hint — warn | text `#fde68a`, bg `rgba(245,158,11,.12)` |
| Cashout glow text | `#ff79c6` |
| Provably fair muted text | `#7a60a0` |

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
