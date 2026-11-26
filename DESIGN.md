# DESIGN.md

> **Status:** Phase 2 — **design system defined.** This is the visual, audio-direction and interaction source of truth. Values here are final unless listed in [§24 Open design decisions](#24-open-design-decisions). Nothing described here is implemented yet; see the Implementation status table in [ARCHITECTURE.md](ARCHITECTURE.md#implementation-status).
>
> **Companion files:** [`client/styles/tokens.css`](client/styles/tokens.css) (every token below, as CSS custom properties), [`art/palette/uog-master.gpl`](art/palette/uog-master.gpl) and [`uog-master.hex`](art/palette/uog-master.hex) (the master palette for Aseprite and other art tools), and [`design/reference/index.html`](design/reference/index.html) (rendered visual reference: palette, type, components, screen mock-ups).
>
> **Precedence:** if code and this document disagree, this document wins until it is deliberately changed. If this document and `tokens.css` disagree, that is a bug. Fix both in the same change.

The goal: **a screen built weeks from now, by a different person or agent, must look like it belongs to the original game.**

---

## Contents

1. [Design philosophy](#1-design-philosophy)
2. [Visual identity](#2-visual-identity)
3. [Art direction](#3-art-direction)
4. [Pixel-art specifications](#4-pixel-art-specifications)
5. [Colour palette](#5-colour-palette)
6. [Typography](#6-typography)
7. [Spacing and layout system](#7-spacing-and-layout-system)
8. [Office environment](#8-office-environment)
9. [Computer interface](#9-computer-interface)
10. [Phone interface](#10-phone-interface)
11. [Character art direction](#11-character-art-direction)
12. [Gameplay HUD](#12-gameplay-hud)
13. [Game screens and additional interfaces](#13-game-screens-and-additional-interfaces)
14. [Reusable components](#14-reusable-components)
15. [Animation guidelines](#15-animation-guidelines)
16. [Interaction states and input](#16-interaction-states-and-input)
17. [Responsive behaviour](#17-responsive-behaviour)
18. [Accessibility and readability](#18-accessibility-and-readability)
19. [Audio direction](#19-audio-direction)
20. [Asset naming conventions](#20-asset-naming-conventions)
21. [Asset export specifications](#21-asset-export-specifications)
22. [Future design expansion](#22-future-design-expansion)
23. [Anti-patterns](#23-anti-patterns)
24. [Open design decisions](#24-open-design-decisions)
25. [Change log](#25-change-log)

---

## 1. Design philosophy

### 1.1 What we are making

A small, hand-made-feeling pixel-art game about a terrible office job. It should look like **a real indie game** with a single authored point of view, not a web app with pixel-art decoration. The office, the computer, the phone and the menus are all **objects inside one fictional world**, drawn on one pixel grid with one palette.

### 1.2 Principles

| # | Principle | What it means in practice |
|---|---|---|
| P1 | **One world, one grid** | World art, UI, portraits, icons and text all share the 480×270 logical pixel grid ([§4](#4-pixel-art-specifications)) and the 64-colour palette ([§5](#5-colour-palette)). |
| P2 | **Interfaces are objects** | The computer is a beige monitor running original in-house software. The phone is a desk phone with an LCD. Reports are paper forms with stamps. No UI is "just a web panel". |
| P3 | **Readable first, funny second** | Controls are plain and unambiguous ("Answer", "Hang up"). The jokes live in flavour text, props, posters, app names and stamps. |
| P4 | **Colour means something** | The palette is mostly tired neutrals. Saturated accents are reserved for meaning: yellow = interactive, green = money/success/answer, red = danger, amber = warning/suspicion, violet = sleep, lime = energy, teal = trust, rose = patience ([§5.4](#54-accent-meanings)). |
| P5 | **Few assets, used well** | Painted background plates, a small prop set, a modular portrait kit and CSS-built UI. Detail goes where the player looks: desk, monitor, phone, faces. |
| P6 | **Motion is stepped and short** | Frame-based animation, 50 ms ticks, 100–300 ms UI transitions, no easing curves, no springs ([§15](#15-animation-guidelines)). |
| P7 | **Honest UI** | Nothing looks interactive unless it is. Unbuilt features are not shown, or are shown locked with a reason. A scripted reply is never presented as AI ([§10.7](#107-voice-text-only-and-degraded-modes)). |

### 1.3 How to use this document

* **Before any visual or audio work**, read the sections you touch, plus [§4](#4-pixel-art-specifications), [§5](#5-colour-palette), [§14](#14-reusable-components) and [§23](#23-anti-patterns). Read ARCHITECTURE.md §4–5 for the technical constraints.
* **Reuse before you create.** Use the tokens in `tokens.css` and the components in §14. If something genuinely new is needed, add it here first (same change is fine), then build it in `client/ui/components/` and add it to the component gallery.
* **MUST** rules are non-negotiable. **SHOULD** rules may be broken only with a written reason in the change description.
* All measurements are **logical pixels** (lpx) on the 480×270 grid unless stated otherwise. In CSS that is always `calc(var(--px) * n)` or a token.
* Coordinates are `(x, y)` from the top-left of the 480×270 game area. Rectangles are written `x0–x1, y0–y1` and are inclusive.

### 1.4 Reference files

| File | Use |
|---|---|
| `client/styles/tokens.css` | The only place raw colours, font names and base sizes appear in code. Components consume semantic tokens. |
| `art/palette/uog-master.gpl`, `.hex` | Load into Aseprite (or any editor) as the only palette. Indices match §5.2. |
| `design/reference/index.html` | Open in a browser. Renders the palette, type specimens, every component state and greybox mock-ups of the key screens at true scale, built only from `tokens.css`. It is a **reference**, not game code; mock-up art is greybox and labelled as such. |

---

## 2. Visual identity

### 2.1 The feeling

A **dingy, fluorescent-lit back office, permanently stuck somewhere around 1989–1997**, run by people who are very proud of very bad ideas. Beige computers with a faint CRT glow, buzzing strip lights, blue-grey carpet tiles, sickly green partition fabric, laminate desks with coffee rings, a coffee machine that is clearly the most important employee, and motivational posters that undercut themselves.

Tone is **dark comedy played straight**: the world takes itself seriously. The humour comes from deadpan corporate copy, absurd props and the gap between corporate cheer and grim reality. It is never mean to the people on the other end of the phone. The joke is on the operation.

### 2.2 The four materials

Every interface is built from four in-world "materials". A new screen MUST pick one of these. It may not invent a fifth.

| Material | Where | Look |
|---|---|---|
| **Putty plastic** | Monitor bezel, phone body, buttons, window chrome, toolbars | Beige faces (`putty-4/5`), 1 lpx ink outline, top/left light bevel, bottom/right dark bevel. Chunky, keycap-like. |
| **Paper** | Window content, forms, memos, reports, index cards, transcript tape, pink slip | Off-white `paper` (or `putty-5` zebra rows), ink text, red index rules, rubber stamps, tractor-feed holes. Flat, no bevel. |
| **LCD** | Phone caller ID, call timer, coffee-machine display, small readouts | Green-grey `wall-4` background, `wall-0` text, `wall-3` ghost segments. |
| **Ink (dark)** | HUD, tooltips, toasts, dark panels, meter wells, letterbox | `ink-1` face, `paper` text, `ink-0` outline. The game's own "overlay" layer, like labels printed on the glass. |

### 2.3 Signature motifs (use these, consistently)

* **Highlighter yellow outline** = "you can interact with this", both in the world and in the UI ([§16](#16-interaction-states-and-input)).
* **Hard dithered drop shadow**: floating panels cast a 2 lpx, 50% checkerboard `ink-0` shadow down-right. Never a blurred shadow.
* **Diagonal hatch** in active window title bars (in-house OS signature; [§9.3](#93-window-chrome)).
* **Manila folder tabs** as the computer's app launcher ([§9.2](#92-desktop-and-launcher)).
* **Rubber stamps** for outcomes (AGREED, HUNG UP, PASSED, FIRED…).
* **Teleprinter tape** for call transcripts ([§10.4](#104-active-call-call-console)).
* **Index cards** with a red rule for records (contacts, staff, initiatives).
* **Pink slip** for the game-over screen.

### 2.4 Pillars (from Phase 1, unchanged)

| Pillar | In practice |
|---|---|
| **Readable first** | Silhouettes and interactables read at a glance at ×2. Clutter is background detail and never competes with interactables. |
| **Limited, moody palette** | Desaturated institutional colours plus a few saturated accents reserved for meaning. |
| **Retro-tech UI** | Plausible, *original* late-80s/90s office software and hardware. No copying of any real operating system or product. |
| **Small, achievable assets** | Few rooms, reusable props, small sprites, a modular portrait kit. |
| **Expandable** | New props, rooms, apps and characters follow [§22](#22-future-design-expansion) without redesign. |

---

## 3. Art direction

### 3.1 Camera and perspective

* **Room scenes ("stage view")**: a straight-on view of a room like a theatre set. The **back wall is flat** and parallel to the screen. The **floor** is a band seen from about 30° above. Furniture uses **oblique projection**: the front face is drawn flat, and the top face is a strip whose depth is drawn at **½ height** (a 16 lpx-deep desk top is drawn 8 lpx tall). There are **no vanishing points**: verticals stay vertical, horizontals stay horizontal, and receding edges go straight up.
* **Close-up scene (`desk`)**: the same oblique rules, from closer. Objects are drawn **bigger**, never scaled. The monitor and phone are the heroes.
* **Depth**: no size change with depth. A character at the back of the floor band is the same size as one at the front. Depth is shown only by y-sorting and overlap.
* **No parallax**, no camera movement and no scrolling rooms. Each location is exactly one 480×270 screen.

### 3.2 Lighting

* **One key light for all world art: overhead fluorescent, slightly front-left.** Light comes from the top-left.
  * Top faces: lightest tone of the ramp.
  * Front faces: base tone.
  * Right-hand faces and undersides: shadow tone.
  * Cast shadows: directly below objects (contact shadow), 1–2 lpx tall, offset 1 lpx right, using the floor ramp's darkest step, or a 50% checkerboard dither of `ink-0` on carpet.
* **Ambient**: cool and slightly green. Rooms never use warm global light, except the evening variant ([§8.6](#86-time-of-day-and-lighting)).
* **Light sources** (monitor glow, coffee machine LEDs, desk lamp) are shown with 1–2 rings of dithered lighter pixels from the same ramp, never with gradients.

### 3.3 Outlines

* **Props, characters, portraits and icons** have a **1 lpx exterior outline** using the **darkest step of the local ramp** (e.g. a wood desk uses `wood-0`, a putty phone uses `putty-0`, skin uses `skin-edge`). Every ramp's darkest step is dark enough to read as an outline.
* **Interior lines** (folds, panel seams) use the shadow step of the ramp, never the outline colour, unless they separate two different materials.
* **Background plates** (walls, floors, ceilings, built-in partitions) have **no exterior outlines** and lower internal contrast. This is the main readability rule: **outlined = foreground or object, unoutlined = background.**
* **Selective outlining**: on the light-facing (top-left) edge of large props (≥ 32 lpx), the outline MAY be broken by 1–3 lpx highlight pixels. Characters and icons always have a closed outline.

### 3.4 Shading

* Each material uses **3 tones + 1 highlight** from its ramp: shadow, base, light, and a sparing highlight.
* **Hard-edged tone steps.** No anti-aliasing against arbitrary colours. Manual AA is allowed only on curves ≥ 8 lpx, and only with an existing intermediate ramp step.
* **No pillow shading.** Light comes from the top-left, so shading follows the form, not the outline.
* **Dithering** is allowed only in these places:
  1. cast shadows on carpet (50% checkerboard),
  2. light falloff from light sources (1–2 rings),
  3. CRT glow at monitor edges,
  4. screen-wide effects: modal backdrop, sleepiness vignette, scene transitions, evening overlay ([§15](#15-animation-guidelines)),
  5. "fuzzy" meter segments ([§14.9](#149-meters-and-progress-bars)),
  6. the dark hard drop shadow on floating UI.

  Patterns are a **2×2 checkerboard (50%)** or a **4×4 Bayer ordered dither (25% / 50% / 75%)**. No noise dither.

### 3.5 Detail and humour in art

* The humour is in **props and text on props**: posters ("TEAMWORK: Because Nobody Can Be Blamed Alone"), sticky notes, a dead office plant, an "Employee of the Month" frame that is empty, the coffee machine's handwritten "DO NOT KICK" sign.
* Readable in-world text is written as pixel art at **≥ 5 lpx cap height** if it must be read, or as 1 lpx scribble lines if it is texture.
* **No real brands, logos or people.** Invented logos must not resemble real ones.
* **Clutter budget**: a room has **at most 8 interactive hotspots** and keeps a clear **silhouette gap** of ≥ 2 lpx between an interactable's outline and other high-contrast detail.

---

## 4. Pixel-art specifications

### 4.1 Logical resolution and scale

| Property | Value |
|---|---|
| Logical resolution | **480 × 270** (16:9) — constants `LOGICAL_WIDTH = 480`, `LOGICAL_HEIGHT = 270` |
| Scaling | **Integer only**, computed in device pixels ([§17](#17-responsive-behaviour)). ×2 = 960×540, ×3 = 1440×810, ×4 = 1920×1080, ×6 = 2880×1620 |
| Pixel density | 1 art pixel = 1 logical pixel = `scale` device pixels. Everything is authored at 1×. |
| Smoothing | Off everywhere: `imageSmoothingEnabled = false`, `image-rendering: pixelated` |

**Why 480×270 and not the Phase 1 suggestion of 640×360:** in a normal *windowed* browser, 640×360 only reaches ×1 on 1366×768 laptops and ×2 on 1080p monitors, leaving about half the screen empty. At 480×270 every common setup gets ×2–×6 and fills 70–98% of the viewport ([§17.2](#172-reference-viewports)). Fewer pixels also keeps the art budget achievable and stops the UI from turning into a dense dashboard.

**MUST:**
* One grid for everything: world, UI, portraits, icons and fonts. No "mixels": no asset is drawn at 2× its own pixel size, and no asset mixes pixel sizes.
* No asset is ever drawn at a non-integer scale, rotated by an arbitrary angle or resampled. If you need a bigger version, draw a bigger version. Horizontal flip is allowed for characters and symmetric props.
* All draw positions are whole logical pixels.
* DOM UI is sized in lpx through `--px`. Borders, gaps and paddings are whole lpx.

### 4.2 Grids

| Grid | Size | Used for |
|---|---|---|
| **UI grid** | 2 lpx (with 1 lpx only for borders and bevels) | Every UI dimension, padding and gap is a multiple of 2, except 1 lpx lines |
| **Layout grid** | 4 lpx | Placement of panels, windows and component groups |
| **Construction grid** | 8 × 8 | Prop footprints, furniture placement, background modules |
| **Tile** | 16 × 16 | Repeating textures: carpet tiles, ceiling tiles, wall panels |
| **Floor band** | rows 140–269 in room scenes | Where characters' feet may stand ([§8.3](#83-scene-composition-rules)) |

Background plates are painted as whole scenes, not built from a tilemap. Tiles are a texture convention, not an engine feature.

### 4.3 Sprite dimensions (fixed)

| Asset type | Frame size (lpx) | Anchor | Notes |
|---|---|---|---|
| Background plate | 480 × 270 | (0,0) | One per layer: `bg-*` and optional `bg-*-fg` (foreground occluder) |
| Standing character | **32 × 56** frame; body **48** tall | bottom-centre (16, 55) | [§11.3](#113-in-world-sprites) |
| Seated character | 32 × 56 frame; body 34 tall | bottom-centre (16, 55) | Feet/chair base on row 55 |
| Portrait | **64 × 64** | (0,0) | [§11.4](#114-portraits) |
| Portrait thumbnail | **32 × 32** | — | **Crop** of the portrait at x 16–47, y 10–41. Never scaled. |
| Small prop | 16 × 16 | bottom-centre | Mugs, clocks, staplers |
| Medium prop | up to 32 × 48 | bottom-centre | Chairs, water cooler, coat rack |
| Large prop | up to 64 × 80 | bottom-centre | Coffee machine, vending machine, fridge |
| Icon (small) | **9 × 9** | — | Inline with text, HUD, buttons |
| Icon (medium) | **16 × 16** | — | App tabs, toolbar, toasts |
| Icon (large) | **32 × 32** | — | Item art, event cards, app headers |
| Cursor | 16 × 16 | per cursor ([§14.21](#1421-cursors)) | Exported pre-scaled ([§21.4](#214-cursors)) |
| Stamp | 96 × 28 (large), 48 × 14 (small) | — | Two separately drawn sizes, never scaled |
| FX | 8 × 8 to 16 × 16 | centre | Steam, Zzz, sparks |

### 4.4 Proportions

* **Adults are 4 heads tall**: head 12 lpx, total 48 lpx (hair and hats may add up to 4 lpx).
* All characters share one body template ([§11.3](#113-in-world-sprites)) and one portrait template ([§11.4](#114-portraits)). Characters vary by silhouette, hair, clothing, colour and accessories, **never by proportion**. The one exception is height: ±4 lpx (44 / 48 / 52) via leg length only.

### 4.5 Animation frame conventions

* **Base tick: 50 ms.** Every frame duration is a multiple of 50 ms. Allowed values: **50, 100, 150, 200, 300, 400, 600, 1000 ms**.
* Animations are frame lists with per-frame durations (Aseprite tags and frame durations, exported as JSON).

| Animation | Frames | Durations | Loop |
|---|---|---|---|
| Character idle (standing) | 2 | 600, 600 (1 lpx breathing bob, torso only) | yes |
| Character walk | 4 | 150 each; moves 2 lpx per 50 ms (40 lpx/s) | yes |
| Character seated typing | 2 | 200, 200 | yes |
| Character seated talking | 2 | 300, 300 | yes |
| Portrait talk | 2 (mouth closed/open) | 150 each while audio or typewriter plays | yes |
| Portrait blink | 1 (eyes closed) | 150, every 3000–6000 ms (random) | — |
| Prop ambient (steam, bubbles, LED) | 2–4 | 150–300 | yes |
| Fluorescent flicker | 3 (on/dim/off) | irregular burst: dim 50, off 50, dim 50, then on for 4000–9000 ms | yes |
| Phone ring shake | 2 | 50, 50 during the ring burst | yes |
| UI icon (hourglass, thinking dots) | 4 / 3 | 200 / 300 | yes |

Frame counts are **upper limits** for v1. More frames need a reason in the change.

---

## 5. Colour palette

### 5.1 Rules

* **MUST:** every pixel of every asset, and every UI colour, comes from the **64-colour master palette** below. The palette check ([§21.2](#212-palette-check)) rejects off-palette exports.
* The palette is **capped at 64**. Adding a colour means removing or merging another, recorded in [§25](#25-change-log).
* Code references **semantic roles** (`--color-*`), never hex. World/art code may reference palette entries (`--pal-*`) where it really needs one.
* No transparency blending at runtime except the cases in [§15.7](#157-screen-effects). Translucency is faked with dithering.

### 5.2 Master palette (64)

Ramps run dark (0) to light. The index is the position in `uog-master.gpl`.

**Ink: cool violet-greys (outlines, dark UI, shadows)**

| Idx | Token | Hex | Use |
|---|---|---|---|
| 00 | `ink-0` | `#14121a` | Universal UI outline, letterbox, meter wells, deepest shadow |
| 01 | `ink-1` | `#221f2b` | Dark UI face (HUD, tooltip, toast), primary text on light |
| 02 | `ink-2` | `#35303f` | Raised dark face, empty meter segment, unknown portrait bg |
| 03 | `ink-3` | `#4a4456` | Bevel light on dark faces, dividers on dark |
| 04 | `ink-4` | `#645d6e` | Muted text on light, disabled text on dark |
| 05 | `ink-5` | `#847d8c` | Disabled text on light, metal mid-tone |
| 06 | `ink-6` | `#a8a2ab` | Muted text on dark, info status, metal light |
| 07 | `ink-7` | `#cdc8c8` | Metal highlight, fluorescent tube body |

**Putty: beige plastic and paper**

| Idx | Token | Hex | Use |
|---|---|---|---|
| 08 | `putty-0` | `#3a3029` | Outline of putty objects |
| 09 | `putty-1` | `#5e5143` | Putty shadow, monitor bezel inner edge |
| 10 | `putty-2` | `#857561` | Putty mid-shadow, secondary button bevel-dark |
| 11 | `putty-3` | `#ad9d80` | Bezel face, inactive title bar, dividers on light, pressed secondary |
| 12 | `putty-4` | `#cfc19f` | Button face, toolbar, phone body |
| 13 | `putty-5` | `#e6dcc0` | Window/panel face, zebra rows |
| 14 | `paper` | `#f6f0dd` | Paper, inputs, light text on dark, bevel-light on putty |

**Carpet: tired corporate blue-grey**

| Idx | Token | Hex | Use |
|---|---|---|---|
| 15 | `carpet-0` | `#1a222c` | Carpet outline/shadow, desktop pattern, primary bevel-dark |
| 16 | `carpet-1` | `#283646` | Carpet shadow, OS desktop, contact portrait bg |
| 17 | `carpet-2` | `#3b4e62` | Carpet base, primary button, active title bar |
| 18 | `carpet-3` | `#55697f` | Carpet light, primary hover, primary bevel-light |

**Wall: sickly green-grey (walls, partitions, LCD)**

| Idx | Token | Hex | Use |
|---|---|---|---|
| 19 | `wall-0` | `#222820` | Partition outline, LCD text |
| 20 | `wall-1` | `#384232` | Wall shadow, coworker portrait bg |
| 21 | `wall-2` | `#555f47` | Wall base, partition fabric |
| 22 | `wall-3` | `#7c8763` | Wall light, LCD ghost segments |
| 23 | `wall-4` | `#a8af86` | Wall highlight, LCD background |

**Wood: laminate, coffee, cardboard, hair**

| Idx | Token | Hex | Use |
|---|---|---|---|
| 24 | `wood-0` | `#2a1b15` | Wood outline, coffee (black), dark hair |
| 25 | `wood-1` | `#4a2f22` | Wood shadow, candidate portrait bg, brown hair |
| 26 | `wood-2` | `#6f4930` | Wood base, coffee with milk |
| 27 | `wood-3` | `#976a45` | Wood light, cardboard, caffeine shade |
| 28 | `wood-4` | `#bf9161` | Wood highlight, caffeine meter, light-brown hair |

**Skin: one shared edge plus four ramps (0 shadow, 1 base, 2 light)**

| Idx | Token | Hex | | Idx | Token | Hex |
|---|---|---|---|---|---|---|
| 29 | `skin-edge` | `#2b1913` | | | | |
| 30 | `skin-a0` | `#b47c5d` | | 36 | `skin-c0` | `#613a24` |
| 31 | `skin-a1` | `#dba886` | | 37 | `skin-c1` | `#875234` |
| 32 | `skin-a2` | `#f3cfae` | | 38 | `skin-c2` | `#ad7149` |
| 33 | `skin-b0` | `#8f5a3b` | | 39 | `skin-d0` | `#3d2318` |
| 34 | `skin-b1` | `#b97d55` | | 40 | `skin-d1` | `#563321` |
| 35 | `skin-b2` | `#dca47a` | | 41 | `skin-d2` | `#74482f` |

**Accents: reserved for meaning ([§5.4](#54-accent-meanings))**

| Idx | Token | Hex | | Idx | Token | Hex |
|---|---|---|---|---|---|---|
| 42 | `hi-0` | `#7d6420` | | 54 | `teal-0` | `#17403f` |
| 43 | `hi-1` | `#e2bd3a` | | 55 | `teal-1` | `#2d8580` |
| 44 | `hi-2` | `#fbe98a` | | 56 | `teal-2` | `#6ccabd` |
| 45 | `amber-0` | `#6e3516` | | 57 | `violet-0` | `#2b2350` |
| 46 | `amber-1` | `#d4732a` | | 58 | `violet-1` | `#5a4a9e` |
| 47 | `amber-2` | `#f2a65a` | | 59 | `violet-2` | `#9c8de0` |
| 48 | `red-0` | `#571820` | | 60 | `lime-1` | `#8fa830` |
| 49 | `red-1` | `#b5333b` | | 61 | `lime-2` | `#c8de6a` |
| 50 | `red-2` | `#e8685d` | | 62 | `rose-1` | `#b04a72` |
| 51 | `green-0` | `#1d4429` | | 63 | `rose-2` | `#e48daa` |
| 52 | `green-1` | `#2f7340` | | | | |
| 53 | `green-2` | `#7cc178` | | | | |

Hair colours come from existing ramps: black/dark brown `wood-0/1`, brown `wood-2/3`, blonde `putty-3/4` + `hi-2` highlight, ginger `amber-0/1/2`, grey/white `ink-5/6/7`, dyed `violet`, `rose` or `teal` (sparingly; one character at most per dyed colour).

### 5.3 Semantic roles

All roles are defined in `tokens.css`. The main ones:

| Group | Token → palette | Notes |
|---|---|---|
| **Backgrounds** | `--color-bg-app` → `ink-0`; `--color-bg-surface` → `putty-5`; `--color-bg-surface-raised` → `putty-4`; `--color-bg-surface-sunken` → `paper`; `--color-bg-dark` → `ink-1`; `--color-bg-dark-raised` → `ink-2`; `--color-bg-well` → `ink-0`; `--color-bg-overlay` → `ink-0` (used only as 50% dither); `--color-bg-zebra` → `putty-5` | Light surfaces are putty/paper; dark surfaces are ink |
| **Office/environment** | Walls `wall-*`, floors `carpet-*`, furniture `wood-*`, equipment `putty-*` and `ink-*`, ceilings `putty-4/5` with `ink-7` tubes | World art uses palette entries directly |
| **Primary** | `--color-primary` → `carpet-2`, hover `carpet-3`, pressed `carpet-1`, text `paper` | The company's corporate blue. One primary per action group. |
| **Secondary** | `--color-secondary` → `putty-4`, hover `putty-5`, pressed `putty-3`, text `ink-1` | Default button |
| **Danger** | `--color-danger` → `red-1`, pressed `red-0`, text `paper` | Hang up, dismiss staff, delete save |
| **Go** | `--color-go` → `green-1`, pressed `green-0`, text `paper` | **Only** the phone's Answer button |
| **Accent/interactive** | `--color-interact` → `hi-1`, `--color-interact-light` → `hi-2`, `--color-interact-dark` → `hi-0` | Hover outline, hotspot outline, selection, focus |
| **Text** | `--color-text` → `ink-1`, `-muted` → `ink-4`, `-disabled` → `ink-5`, `-inverse` → `paper`, `-inverse-muted` → `ink-6`, `-inverse-disabled` → `ink-4`, `-link` → `carpet-2` (always underlined) | |
| **Status** | success `green-2` (on dark) / `green-1` (on light); warning `amber-2` / `amber-0`; danger `red-2` / `red-1`; info `ink-6` / `ink-3` | Always paired with an icon or label |
| **Money** | positive `green-2` / `green-1`; negative `red-2` / `red-1` | Negative amounts also get a minus sign |
| **Energy** | fill `lime-2`, shade `lime-1`; low (≤ 30) `amber-2/1`; critical (≤ 10) `red-2/1` | |
| **Sleepiness** | fill `violet-2`, shade `violet-1`; vignette uses `ink-0` dither | Colour stays violet; urgency comes from thresholds and effects |
| **Caffeine** | fill `wood-4`, shade `wood-3` | Coffee coloured |
| **Trust** | fill `teal-2`, shade `teal-1` | |
| **Suspicion** | fill `amber-2`, shade `amber-1` | Suspicion is a warning, so it shares the warning hue |
| **Patience** | fill `rose-2`, shade `rose-1` | |
| **Progress** | fill `paper`, shade `ink-6` | Quota, research, rings remaining |
| **Lines** | outline `ink-0`; soft divider `putty-3` (light) / `ink-3` (dark); bevels per surface | |
| **Computer OS** | bezel `putty-3`; desktop `carpet-1` with `carpet-0` pattern; active title bar `carpet-2` + `carpet-1` hatch; inactive `putty-3` + `putty-2` hatch | |
| **Phone** | body `putty-4`; LCD `wall-4` / `wall-0` / `wall-3`; ring LED `red-2`, off `ink-3`; tape `paper` with `putty-3` holes | |
| **Portrait backgrounds** | contact `carpet-1`; coworker `wall-1`; boss `red-0`; candidate `wood-1`; unknown `ink-2` | [§11.6](#116-portrait-backgrounds-and-role-colours) |
| **Documents** | pink slip `rose-2`; boss letterhead `red-1`; stamps good `green-1`, bad `red-1`, neutral `carpet-2` | |

### 5.4 Accent meanings

The saturated ramps are **reserved**. Using them for decoration in UI is a defect. World art may use them on props only where the meaning holds (a red fire extinguisher is fine; a red decorative stripe on a calm panel is not).

| Hue | Means | Never use for |
|---|---|---|
| Yellow `hi-*` | Interactive, focus, selection, "look here" | Warnings, decoration |
| Green `green-*` | Money, success, Answer | Energy (that is lime) |
| Red `red-*` | Danger, error, strike, boss authority | Decoration |
| Amber `amber-*` | Warning, suspicion, low energy | Interactivity |
| Violet `violet-*` | Sleepiness, night | — |
| Lime `lime-*` | Energy | Money |
| Teal `teal-*` | Trust | Primary buttons |
| Rose `rose-*` | Patience, pink slip | — |

### 5.5 Contrast (WCAG 2.x, measured)

| Pair | Ratio | Use |
|---|---|---|
| `paper` on `ink-1` | 14.2 | HUD text, tooltips, toasts |
| `ink-6` on `ink-1` | 6.5 | Muted text on dark |
| `ink-1` on `putty-5` | 11.8 | Body text in windows |
| `ink-1` on `paper` | 14.2 | Inputs, tape, forms |
| `ink-4` on `putty-5` | 4.6 | Muted text on light |
| `ink-4` on `paper` | 5.5 | Muted text on paper |
| `ink-1` on `putty-4` | 9.1 | Secondary button label |
| `paper` on `carpet-2` | 7.5 | Primary button, title bar |
| `paper` on `carpet-3` | 5.0 | Primary hover |
| `paper` on `red-1` | 5.3 | Danger button |
| `paper` on `green-1` | 5.1 | Answer button |
| `wall-0` on `wall-4` | 6.6 | LCD text |
| `hi-1` on `ink-1` | 8.9 | Highlighted label on dark |
| `red-2` on `ink-1` | 5.1 | Danger text on dark |
| `amber-2` / `green-2` / `teal-2` / `violet-2` / `lime-2` / `rose-2` on `ink-1` | 8.0 / 7.5 / 8.3 / 5.6 / 10.9 / 6.7 | Meter fills and status text on dark (non-text contrast ≥ 3 met) |
| `ink-5` on `putty-5` | 2.9 | **Disabled only** (exempt), always with hatch or lock icon |

**Rules:** body and label text ≥ 4.5:1. Meter fills, icons and focus indicators ≥ 3:1 against their background. The `-1` accent steps (`red-1`, `violet-1`, `teal-1`, `green-1`) are below 3:1 on `ink-1`, so they are **only** used as the shade row of a fill or on light backgrounds, never as a fill on dark.

---

## 6. Typography

### 6.1 Families (two, plus one accessibility fallback)

| Role | Family | Licence | Native size (pixel-exact) | Used for |
|---|---|---|---|---|
| **UI / body / headings / numbers** | **Departure Mono** (Helena Zhang) | SIL OFL 1.1 | **11 lpx** font-size; 7 lpx advance, ~7 lpx capitals (measured) | All interface text: labels, headings, body, transcripts, tables, HUD, LCD, buttons |
| **Title** | **Jersey 15** (Sarah Cadigan-Fried, Google Fonts) | SIL OFL 1.1 (record the bundled `OFL.txt` when the file is added) | **27 lpx** font-size = **15 lpx capitals**, 11 lpx x-height, 11 lpx digit advance (measured) | Day cards, title screen, game over, **form headers** (CALL REPORT, PERFORMANCE REVIEW, ON BREAK…), the big quota number |
| **Readable fallback** (setting only) | **Atkinson Hyperlegible Next** (Braille Institute) | SIL OFL 1.1 | 11 lpx, line 16 (not a pixel font; sized on the grid) | Replaces Departure Mono for **running text** when "Readable font" is on ([§18.4](#184-text-options)) |

Why these:
* **Departure Mono** is monospaced, so digits are tabular for free (money, timers, ledgers), and transcript speaker labels line up. It reads like an office terminal, has strong `0/O` and `1/l/I` shapes and broad glyph coverage, and is OFL. One UI family keeps every screen visually related.
* **Jersey 15** is a blocky, slightly condensed pixel display face. In the Jersey family **the number is the cap height**: Jersey 15 is pixel-exact only at a 27 px font-size (15 px capitals). The other sizes are either fractional or 2× copies (mixels). Jersey 15 at 27 lpx is the only large type in the game, so big text stays on the 1 lpx grid instead of being Departure Mono at 2×.
* Rejected after measurement (Phase 2): Jersey 25 (41 px native, far too big), Jersey 10 (no integer native size), Silkscreen/Tiny5/Micro 5 (5 lpx capitals: too small for headings), VT323 and Pixelify Sans (not pixel-exact at a useful size).
* The game loads **no other fonts**. Fonts are self-hosted from `client/public/assets/fonts/`. Licences go in `client/public/assets/fonts/LICENSES.md` and in the in-game credits.

**Native-size rule (MUST):** each pixel font is used **only at its native size**: Departure Mono at 11 lpx, Jersey 15 at 27 lpx. No other sizes. No browser-synthesised bold or italic: set `font-synthesis: none`. Letter-spacing is always 0. Text is positioned on whole lpx. Set `-webkit-font-smoothing: none` where supported.

> **Measured (Phase 2, Chromium, `design/reference/index.html` font check):** Departure Mono at 11 px has an advance of exactly 7.00 for every glyph. Jersey 15 at 27 px gives integer advances for all tested glyphs, with 15 px capitals. Phase 3 repeats the check in Firefox and WebKit and confirms glyph coverage for `…`, `·`, `‹` ([§24](#24-open-design-decisions) D12).

### 6.2 Type scale

There are exactly **five text styles**. No other combinations.

| Style token | Family / size | Line height | Case | Use |
|---|---|---|---|---|
| `text-title` | Jersey 15 / 27 | 28 | Capitals | Day card "DAY 04", "TERMINATION NOTICE", form headers ("CALL REPORT"), title screen, quota number "3/8" |
| `text-heading` | Departure Mono / 11 | 12 | **CAPITALS**, `--color-text` (full contrast) | Window titles, modal titles, section headings, name plates, scene signs; always on a title strip or above a 1 lpx rule |
| `text-label` | Departure Mono / 11 | 12 | **CAPITALS**, usually muted | HUD labels, column headers, field labels, LCD rows |
| `text-ui` | Departure Mono / 11 | 12 | Sentence case | Buttons, list rows, inputs, tabs, tooltips (single line), names |
| `text-body` | Departure Mono / 11 | 14 | Sentence case | Paragraphs, memos, transcripts, multi-line tooltips, descriptions |

* **Hierarchy without sizes:** below the title, hierarchy comes from **case** (capitals for headings and labels), **contrast** (heading `ink-1` vs label `ink-4`), **placement** (title strips, rules, plates) and **inverse plates** (`ink-1` plate with `paper` text). A screen uses at most one `text-title`.
* **Weights:** one weight (400) per family. Emphasis comes from colour (`--color-text` against `-muted`), capitals, an inverse highlight bar (`hi-2` background with `ink-1` text), or a 1 lpx underline. **Never bold.**
* **Numbers and counters:** Departure Mono, except the quota fraction and day number on cards (`text-title`). Money is right-aligned in tables. Thousands separator is `,`. There are no decimals (money is an integer). Times use 24-hour `10:42`. Durations use `MM:SS` (`03:12`). Days are zero-padded to two digits: `DAY 03`, then `DAY 100`. A negative amount is `-1,240` in the negative-money colour. The currency is a 9×9 icon (`icon-money`), never a glyph.
* **Advance width:** Departure Mono advances **7 lpx per character** (measured). Layout budgets in this document use 7 lpx per character. Jersey 15 is proportional (digits 11 lpx, `i` 5 lpx, `M`/`W` 17 lpx). Budget about 11 lpx per character for capitals.

### 6.3 Text rules

* Left-aligned by default. Centred only in title cards, stamps, and buttons.
* Line length ≤ 44 characters in body text (≈ 308 lpx).
* Long text truncates with `…` on single-line elements and shows the full text in a tooltip. Body text wraps and never truncates.
* Capitals for more than four words only for in-world signage and stamps.
* **Copy voice:** in-world copy is deadpan corporate ("Initiatives", "Procurement", "Card File"). **Controls stay literal**: Answer, Decline, Hang up, Send, Buy, Hire, Dismiss, Save, Resume, Quit to title. A joke may sit next to a control, never inside its label.
* AI dialogue is plain text, rendered as text nodes, never HTML or Markdown (ARCHITECTURE.md §7.6).

---

## 7. Spacing and layout system

### 7.1 Spacing scale

Tokens are named by their logical-pixel value. **Only these values** are used for padding, gaps and margins.

| Token | lpx | Typical use |
|---|---|---|
| `--sp-1` | 1 | Borders, bevels, hairlines only (never padding) |
| `--sp-2` | 2 | Icon-to-text gap in dense rows, tooltip vertical padding, toast stack gap |
| `--sp-4` | 4 | Default gap between related controls, compact panel padding, screen edge margin |
| `--sp-6` | 6 | Default panel padding, button horizontal padding |
| `--sp-8` | 8 | Gap between groups, modal padding |
| `--sp-12` | 12 | Gap between sections within a window |
| `--sp-16` | 16 | Large section separation |
| `--sp-24` | 24 | Title-card spacing |
| `--sp-32` | 32 | Title-card spacing |

**Rules:**
* Inside a component: 2/4/6. Between components: 4/8. Between sections: 12/16.
* Text baselines inside rows are vertically centred. A 12 lpx line in a 14 lpx row sits 1 lpx from the top and 1 lpx from the bottom.
* **Screen safe margin: 4 lpx** from the game-area edge for every UI element except full-bleed screens (computer bezel, title cards).

### 7.2 Screen regions (480×270)

```
  0                                                                     479
0 ┌──────────────────────────────────────────────────────────────────────┐
  │ HUD BAR  y 0–19  (always on top; world art here is ceiling only)       │
20├──────────────────────────────────────────────────────────────────────┤
  │ loc. label (4,24)                           TOAST STACK x 316–475,     │
  │                                             y 24→ (max 3)              │
  │                                                                        │
  │             WORLD / SCREEN AREA  y 20–269                              │
  │  exit tab                                                   exit tab   │
  │  x 0–13                                                     x 466–479  │
  │                                                                        │
  │                                          PHONE TRAY x 332–475,         │
  │ save indicator (4,256)                   y 148–265 (when ringing)      │
269└──────────────────────────────────────────────────────────────────────┘
```

| Region | Rect | Rule |
|---|---|---|
| HUD bar | 0–479, 0–19 | Always visible, except on title, day card and game-over screens |
| Location label | from (4, 24), 1 line | Shown for 1500 ms on arrival |
| Toast stack | 316–475, from y 24 | Grows downward, max 3 |
| Exit tabs | left x 0–13, right x 466–479 (y 164–195); bottom: centred on x 240, y 254–269 | Fixed positions in every room |
| Phone tray | 332–475, 148–265 | Incoming and outgoing call panel |
| Save indicator | from (4, 256) | Bottom-left, transient |
| Modal area | centred in 0–479, 20–269 (centre point (240, 145)) | |
| Dialogue box | 4–475, 194–265 | In-person conversations |

### 7.3 Placement conventions (consistent everywhere)

* **Close** is always at the **top-right** of a window or modal (11×11 close box, 2 lpx inside the title bar).
* **The primary action is at the bottom-right.** Secondary actions sit to its left with a 4 lpx gap. Cancel/Back is the leftmost action in the group.
* **Destructive actions** never sit in the primary slot of a dialog that opened for something else, and they always confirm ([§14.1](#141-buttons)).
* Form labels go **above** fields (`text-label`, 2 lpx gap).
* Lists sit on the left and detail on the right.
* Toasts appear top-right. Tooltips appear below-right of the pointer.

### 7.4 Layers (z-order)

| Layer | Token | Content |
|---|---|---|
| World | `--z-world` 0 | Canvas: background, props, characters, hotspot highlights |
| Hotspots | `--z-hotspots` 10 | Transparent accessible `<button>`s |
| HUD | `--z-hud` 20 | HUD bar |
| Screen | `--z-screen` 30 | Computer, phone call screen, dialogue box, phone tray |
| Effects | `--z-effects` 35 | Sleepiness vignette and eyelids, dozing (DOM overlay above screens, below modals) |
| Modal | `--z-modal` 40 | Modals and their dithered backdrop |
| Toast | `--z-toast` 50 | Toasts |
| Tooltip | `--z-tooltip` 60 | Tooltips |
| System | `--z-system` 70 | Too-small-window notice, fatal error |

The HUD must stay readable during sleepiness effects, so **the HUD is redrawn above the effects layer**: the effects layer masks rows 0–19.

---

## 8. Office environment

### 8.1 Building plan and location graph

The office is one floor of a cheap commercial unit. Plan (not to scale):

```
┌──────────────┬───────────┬──────────────────────────────┐
│ BOSS-OFFICE  │ SUPPLY-   │ BREAK-ROOM                   │
│ desk, window │ CLOSET    │ coffee machine, fridge,      │
│ trophy shelf │ (Phase 8) │ vending, notice board, table │
├────door──────┴───door────┼──────────doorway─────────────┤
│ OFFICE-FLOOR             │ RECEPTION (Phase 7)          │
│ 5 cubicles, water cooler │ front door, time clock,      │
│ [YOUR DESK] ──► DESK     │ empty reception desk         │
└──────────────────────────┴──────────────────────────────┘
```

```
desk ── office-floor ─┬─ break-room
                      ├─ boss-office
                      ├─ reception      (Phase 7)
                      └─ supply-closet  (Phase 8)
```

| Location id | Phase | Purpose | Key hotspots (≤ 8) |
|---|---|---|---|
| `desk` | 3 | The hub: close-up of the player's desk | Computer, phone, mug, in-tray (memos shortcut), coat rack (evening: go home), exit "Office floor" |
| `office-floor` | 3 | Navigation hub, ambience, boss walk-bys | Your desk, boss office door, break room doorway, water cooler (later), coworkers (later), exits |
| `break-room` | 3 | Coffee | Coffee machine, fridge (flavour), notice board (events), vending machine (later), exit |
| `boss-office` | 3 (scene) / 7 (briefing, review) | Morning briefing, reviews | Boss, chair, performance chart (flavour), exit |
| `reception` | 7 | Clock in/out, deliveries | Time clock, front door, exit |
| `supply-closet` | 8 | Equipment upgrades shown physically | Shelves, server cabinet, exit |

Locations that are not built yet are **not shown as exits**. A doorway may exist in the art, but it gets no hotspot and no exit tab.

### 8.2 Scene layouts

All rooms share the same vertical structure:

| Band | Rows | Content |
|---|---|---|
| Ceiling | 0–19 | Ceiling tiles, fluorescent fixtures. Under the HUD; **never interactive**. |
| Back wall | 20–139 | Doors, windows, posters, clock, shelves. Wall-mounted hotspots live here. |
| Floor band | 140–269 | Furniture, props and characters. Feet on rows 150–262. |

**`office-floor`** (stage view, left to right):

| x range | Element |
|---|---|
| 16–56 | Boss office door: frosted glass upper half, door plate "MANAGEMENT". Hotspot. |
| 60–116 | Interior window with half-closed blinds. Shows the boss silhouette when the boss is in. |
| 124–156 | Supply closet door (background only until Phase 8). |
| 176–216 | Motivational poster (40×52, 3 variants). |
| 222–238 | Wall clock (16×16, hour frames). |
| 252–316 | Exterior window (64×48 time-of-day sprite) with a sad blind. |
| 340–360 | Water cooler (20×44, bubble animation). |
| 392–440 | Doorway to the break room. Hotspot. |
| 448–476 | Exit sign above a corridor toward reception (Phase 7). |
| Back row, y 118–165 | Two cubicles (x 150–230, 240–320) with seated coworkers' heads and shoulders above the partitions. |
| Front row, y 172–228 | Three cubicles: x 70–170 (abandoned, dead plant), **x 190–290 the player's cubicle** (empty chair, monitor glow; hotspot "Your desk"), x 300–400 (coworker). |
| Front aisle, feet y 248–258 | Boss walk-by path, entering left and exiting right. |

**`desk`** (close-up; the player is seated):

| Rect | Element |
|---|---|
| 0–479, 20–169 | Partition wall (wall ramp) with pinned memos, a calendar and a cat photo |
| 0–479, 170–269 | Desk top (wood laminate; 8 lpx top strip, then front face) |
| 120–327, 36–191 | **Monitor** (bezel 208×156 incl. stand). Hotspot "Use computer". The screen area shows the current desktop as a 16-colour miniature (static sprite, 2 frames of glow). |
| 344–439, 176–231 | **Desk phone** (96×56). Hotspot "Phone". Handset, LCD strip, ring LED. |
| 56–71, 194–209 | Mug (16×16: empty / full / steaming). Flavour only; coffee is in the break room. |
| 8–47, 200–219 | In-tray (memo shortcut, badge when unread) |
| 448–467, 60–115 | Coat rack (evening only: "Go home") |
| 184–295, 254–269 | Exit tab "Office floor" (bottom, 112 wide) |

**`break-room`**: coffee machine as the hero (48×64 at x 96–143, feet y 200), counter, fridge (32×64) covered in passive-aggressive notes, vending machine (40×68), small table with two chairs, cork notice board (56×40) on the back wall, and a doorway left (exit to the office floor).

**`boss-office`**: boss desk centred (feet y 220), boss seated behind it (seated sprite) or standing, a visitor chair in the foreground with the **player's back** when in a meeting, a window with blinds, a trophy shelf ("SHARK OF THE MONTH"), a framed sales chart going down, a large plant (the only healthy plant in the office). Exit tab right.

### 8.3 Scene composition rules

1. Interactables sit **between y 40 and y 262**, never in the ceiling band, and never under a fixed UI region while that UI is showing (toast stack, phone tray).
2. Draw order: `bg` → props sorted by anchor y → characters sorted by anchor y → `bg-*-fg` occluder (partition fronts, desk fronts) → FX → hotspot highlight.
3. Every interactable has a hotspot rectangle (or a list of them) that **matches its silhouette within 2 lpx**. Hit areas are at least 16×16 lpx; small props get a padded rectangle.
4. At most **8 hotspots** per scene, including exits.
5. Each room has **one** ambient motion source in view at a time besides characters (a flickering tube, steam, a blinking LED). Room loops never sync. Durations are offset by ≥ 100 ms.

### 8.4 Interactive indicators

| State | World rendering | DOM/hotspot |
|---|---|---|
| Idle | Nothing. Interactables read through outline and contrast alone. | Transparent button |
| Hover | **1 lpx `hi-1` outline** traced outside the sprite's own outline (a 1 lpx dilation of the alpha mask) + tooltip "Name — verb" (e.g. "Coffee machine — Make coffee") + `cursor-interact` (or an exit cursor) | — |
| Keyboard focus | Hover outline **plus focus brackets**: four 3×3 L-shaped corners in `hi-1`, 2 lpx outside the hotspot bounding box, + tooltip anchored below | Focus ring hidden (the canvas draws it) |
| Pressed | Outline turns `hi-2` for 100 ms | — |
| Unavailable | 1 lpx `ink-5` outline on hover/focus; tooltip gives the reason ("Coffee machine — Brewing, 2 min"); `cursor-disabled` | `aria-disabled="true"` with the reason as its description |
| "Show interactables" (hold `H`) | All available hotspots show a `hi-0` outline and the focused one shows `hi-1`, while held | — |

### 8.5 Exits and navigation

* **Exit tabs** are fixed UI, not art. Side tabs are 14×32 at the floor-level midpoint (left x 0–13 / right x 466–479, y 164–195). The bottom tab is 16 tall at y 254–269, centred on x 240, and as wide as its content: 1 + 6 + 9 (arrow) + 4 + 7 × characters + 6 + 1 ("Office floor" = 112). Each is an `ink-1` tab with an `ink-0` outline and a 9×9 arrow icon in `paper`. The bottom tab also shows the destination name. Side tabs show it in their tooltip and in "show interactables" mode.
* Doorways in the art are **also** hotspots to the same destination.
* Arriving in a location shows the **location label** (`text-label`, `ink-1` plate, `paper` text) at (4, 24) for 1500 ms.

### 8.6 Time of day and lighting

| Phase | Clock | Treatment |
|---|---|---|
| Morning | 09:00–11:59 | Window sprite frame `morning` |
| Midday | 12:00–14:59 | Window `midday` |
| Afternoon | 15:00–16:59 | Window `afternoon` |
| Late / evening | ≥ 17:00 and the evening phase | Window `late`; global overlay: 25% Bayer dither of `ink-0` over the world (not UI); desk lamp glow on `desk`; fluorescents in the back half of `office-floor` switched off (`off` frame) |

There are no palette swaps per time of day in v1.

### 8.7 Furniture and prop design rules

* **Era:** beige towers and CRTs, chunky desk phones with LCD strips, laminate desks with rounded front edges, swivel chairs with one missing caster, metal filing cabinets, cork boards, wall clocks, water cooler, a vending machine with one stuck item.
* **Condition:** everything is slightly worn. Use a 1–2 px stain, chip or sticky note per prop, **never more than 3 "joke" details per prop**.
* **Materials map to ramps:** desks `wood`, partitions and walls `wall`, carpet `carpet`, equipment `putty`, metal `ink-4..7`, paper `paper`/`putty-5`.

---

## 9. Computer interface

The computer runs an **original, unnamed in-house operating system**, the kind of thing a cheap company bought in 1991 and never updated. Its identity: putty window chrome, paper content areas, a blue-grey desktop with a dithered pattern, **diagonal-hatch title bars**, and **manila folder tabs** instead of a start menu or desktop icons. It must not resemble any real OS's distinctive layout, logo, iconography or sounds.

### 9.1 Screen frame

| Element | Rect | Spec |
|---|---|---|
| HUD | 0–479, 0–19 | Unchanged; time keeps running |
| Monitor bezel | 0–479, 20–269 | `putty-3` with a 1 lpx `putty-1` inner edge. Sides 6 lpx, top 4, bottom 6. Power LED 3×2 `green-2` at (452, 265). |
| Screen | 6–473, 24–263 (468×240) | Desktop + windows. CRT edge glow: a 1 lpx ring of 50% dither `carpet-3` just inside the screen edge. No scanlines over text. |
| Window area | 6–473, 24–245 | Maximised app window |
| Folder-tab bar | 6–473, 246–263 (18 tall) | App launcher + Log off |

**Opening**: from the desk, activate the monitor. The CRT power-on transition ([§15.4](#154-scene-and-screen-transitions)) runs, then the desktop or the last open app appears. **Closing**: the `Log off` button, `Esc` when no app has focus, or clicking the bezel. Closing returns to the `desk` scene.

### 9.2 Desktop and launcher

* **Desktop:** `carpet-1` with a `carpet-0` 4×4 Bayer 25% pattern. Bottom-right shows the company slogan in `text-heading`, `carpet-3`: "RESULTS ARE A STATE OF MIND." Top-left shows the desktop note (a paper sticky, 96×40) with the empty-state hint: "No folder open. Pick one below."
* **Folder tabs (launcher):** one tab per registered app, in manifest `order`.

| Part | Spec |
|---|---|
| Inactive tab | 26×16, x-gap 2, top-aligned at y 248. Face `--color-os-tab` (`putty-4`), 1 lpx outline, 2 lpx rounded *pixel* corners at the bottom (drawn: the corner pixel is omitted). 16×16 app icon centred. |
| Active tab | Shows the 16×16 icon + title (`text-ui`). Width = 6 + 16 + 4 + text + 6. Face `--color-os-tab-active` (`putty-5`). Height 18 (extends up 2 lpx and **joins the window's bottom edge**, with no outline between them). |
| Hover / focus | Outline `hi-1` / focus ring. Tooltip: app title. |
| Badge | Unread count badge ([§14.8](#148-badges)) on the tab's top-right corner |
| Locked app | Tab shows `icon-lock` instead of the app icon, with `ink-5` icon tint frames. Tooltip: "Personnel — Locked: available from day 5". Clicking opens the app's **locked state** (never a fake UI). |
| Log off | Secondary button at the right end: x 406–471 (66 wide), y 248–263, label "Log off" |

Keyboard: `Tab` reaches the tab bar. `←/→` move between tabs and `Enter` opens one. `Ctrl+1…7` (when no text field is focused) jumps to the app in that position.

### 9.3 Window chrome

v1 windows are **maximised** inside the window area. There is no dragging, no resizing and no minimise. These controls are **not drawn** ([§23](#23-anti-patterns)). An app may open **one** child dialog at a time (a modal inside the screen).

| Part | Rect (maximised) | Spec |
|---|---|---|
| Frame | 6–473, 24–245 | 1 lpx `ink-0` outline + 1 lpx bevel (`paper` top/left, `putty-3` bottom/right) |
| Title bar | 8–471, 26–39 (14 tall) | Active: `carpet-2` with **diagonal hatch** (1 lpx `carpet-1` lines at 45°, every 3 lpx) filling the space right of the title, with a 4 lpx gap. Inactive (a child dialog is open): `putty-3` + `putty-2` hatch, title `ink-2`. |
| Title icon | (10, 28) | 9×9 app icon (small variant) |
| Title text | from (22, 27) | `text-label`, `paper` |
| Close box | (459, 27) 11×11 | `putty-4` face, 1 lpx `ink-0` outline, 5×5 `×` glyph sprite in `ink-1`. Hover/focus/pressed per [§16](#16-interaction-states-and-input). Label: "Close <App>". |
| Toolbar row | 8–471, 40–55 (16) | `putty-4`. Section tabs ([§14.13](#1413-tabs)), filters, search input, primary action at the right |
| Content | 8–471, 56–231 (464×176) | `paper` or `putty-5` per app template |
| Status bar | 8–471, 232–243 (12) | `putty-4`, 1 lpx `putty-3` top line. `text-ui` in `ink-4`: counts left, "Updated 10:42" (game time) right |

Child dialog: a modal ([§14.6](#146-modals)) sized small or medium, centred on the screen area, with its own title bar. The parent title bar goes inactive.

### 9.4 App template

Every app uses one of three content layouts. All share header, toolbar and status bar.

**A. List / detail** (Card File, Memos, Personnel, Initiatives)

```
 x 8                     175 178                                     471
 ┌───────────────────────┐ ┌───────────────────────────────────────────┐ y 56
 │ list (168 wide)        │ │ detail (294 wide)                         │
 │ rows 14 or 28 or 36    │ │ index card / paper document               │
 │ selected row: hi-2 bg  │ │ padding 6                                 │
 │ scrollbar 7 at right   │ │ actions bottom-right                      │
 └───────────────────────┘ └───────────────────────────────────────────┘ y 231
```

**B. Table** (Ledger, Procurement catalogue): a full-width table with a 14 lpx header row (`text-label`, `putty-4` bg) and 14 lpx rows zebra-striped `paper` / `putty-5`. Numeric columns are right-aligned. Sort affordance: a 5×5 up/down arrow sprite after the header label and `hi-1` underline on the sorted column.

**C. Document** (Today, Day close, forms): a centred paper sheet up to 320 wide, padded 8, with a 2 lpx dithered shadow on a `putty-5` field.

**Shared app states (MUST, built from shared components [§14.16](#1416-state-panels-loading-empty-error-locked)):**

| State | Presentation |
|---|---|
| Opening (lazy chunk loading) | Shell shows the window frame + `state-loading`: hourglass icon (4 frames × 200 ms) + "Opening folder…". After 1 s it adds "(The mainframe is thinking.)". After the timeout it shows the error state. |
| Loading data | Same panel inside the content area with app-specific copy |
| Empty | `state-empty`: 32×32 app illustration (the large app icon) + one-line deadpan description + what to do next (button if there is an action) |
| Error | `state-error`: `icon-danger` + "Paper jam." + what failed in plain words + [Try again] |
| Locked | `state-locked`: `icon-lock` (32×32 large) + "Locked" + the unlock condition in plain words. No fake data behind it. |
| Offline/degraded (AI-backed views) | Inline banner at the top of the content: `ink-1` strip, `amber-2` icon, "Lines degraded — scripted replies only." |

### 9.5 Notifications inside the computer

* App events raise a **badge** on the folder tab and, if important, a **toast** (the same toast system as everywhere; [§14.7](#147-toasts)).
* Boss memos also make the **desk in-tray** prop show a badge when the player is in the `desk` scene.
* No pop-up windows interrupt the player except: incoming calls (phone tray) and critical modals (fired, caught asleep).

### 9.6 Applications

App ids are fixed (ARCHITECTURE.md §4.5). Titles are in-world copy.

#### `dashboard`: **Today** (Phase 4)

* **Purpose:** what you must do today and how it's going.
* **Layout:** Document template, two columns inside the sheet.
  * Left (180 wide): **Quota card**: an index card showing "TODAY'S QUOTA", the metric name, a big `text-title` fraction "3/8", a continuous progress bar 168×5, and time left until 17:00. Below it, **Objectives**: up to 4 checklist rows (9×9 checkbox sprite: empty / ticked `green-1` / failed `red-1`, + `text-ui` description + `3/5` progress right-aligned).
  * Right (264 wide): **From Management**: the latest boss memo excerpt (paper with a red 4 lpx letterhead stripe, 3 lines) + [Open memo] link button. **Day timeline**: a 248×12 strip from 09:00 to 17:00 with hour ticks, a `hi-1` 1×12 "now" marker, and past calls as 3×5 coloured pips (green good / amber neutral / red bad).
* **Hierarchy:** quota number > objectives > memo > timeline.
* **Interactions:** clicking an objective shows its tooltip. Clicking a timeline pip opens that call's report in Card File.
* **States:** never empty during work. Evening shows the "Day closed" summary. Loading and error use the shell states.

#### `contacts`: **Card File** (Phase 4 shell / 5 data)

* **Purpose:** everyone you can call and what you know about them.
* **Layout:** List/detail. Toolbar: tabs **All / Regulars / New / Burned**, search input (120 wide), sort toggle (Name / Last call).
* **List rows:** 28 lpx two-line: line 1 name (`text-ui`), line 2 relationship + last contact ("Regular · day 3") in `ink-4`. A 5×5 status LED at the right (green callable, amber call-back requested, `ink-3` blocked). Selected row: `hi-2` background.
* **Detail:** an **index card**: portrait 64×64 in its frame (role background), display name (`text-heading`), occupation and town (fictional), **What we know** (fact list, `text-body`; unknown fields shown as "???"), **Relationship** meters (trust/suspicion/patience, large, fuzzy per game rules), **Call history** (last 5: day, duration, small stamp sprite). Actions at the bottom-right: **[Call]** primary. It is disabled with a reason when blocked ("Not taking your calls until day 6").
* **Empty:** "The card file is empty. New leads arrive with the morning post."
* **Search empty:** "No cards match "{query}"." + [Clear search]

#### `mail`: **Memos** (Phase 4)

* **Purpose:** messages from Management, coworkers and events.
* **Layout:** List/detail. Rows are 28 lpx: unread 5×5 `hi-1` dot, sender (`text-ui`), subject (muted, truncated), time right-aligned.
* **Detail:** a paper memo: header block (`TO:`, `FROM:`, `RE:`, `DAY:` in `text-label` + values), 1 lpx rule, body `text-body`. Boss memos get the red 4 lpx letterhead stripe and "FROM THE DESK OF MANAGEMENT". Optional action buttons at the bottom-right (e.g. [Accept] on event memos).
* **Empty:** "No memos. Enjoy it while it lasts."

#### `finances`: **Ledger** (Phase 8)

* **Purpose:** the money: balance, today's income and costs, payroll due.
* **Layout:** a summary strip (3 index-card mini panels, 148×36 each: Balance, Today, Payroll due), then a Table on **fanfold paper**: tractor-feed holes (2×2 `putty-3` every 8 lpx) in 6 lpx margins left and right, zebra rows. Columns: Time (35) · Memo (flex) · Category (63) · Amount (63, right). Toolbar tabs: **Today / This week / Day close**.
* **Day close** sub-view: a printed summary, Document template, totals by category, net result with a stamp (PROFIT `green-1` / LOSS `red-1`).
* **Empty:** "No transactions yet today. The day is young and so is the debt."

#### `research`: **Initiatives** (Phase 8)

* **Purpose:** start research projects that unlock modifiers.
* **Layout:** List/detail. Rows are 28 lpx: name + state line ("2h 10m left" / "Available" / "Requires: Cold Calling 101"). Only one is active at a time, pinned to the top with a continuous progress bar under its name.
* **Detail:** index card with description, effect in plain numbers ("Calls drain 10% less energy"), cost, duration, requirements (lock icons), and **[Start initiative]** primary (disabled with a reason if another is active or funds are short). Completed initiatives show the COMPLETED stamp.

#### `shop`: **Procurement** (Phase 8)

* **Purpose:** buy equipment and office upgrades.
* **Layout:** Table-like catalogue. Rows are 36 lpx: 32×32 item icon, name + one-line effect, level pips (5×5 each: filled `paper`, empty `ink-3`, on an `ink-1` well), price right-aligned with `icon-money`, and an **[Order]** button. Toolbar tabs: **Desk / Break room / Phones / Office**.
* **Purchase:** [Order] is a **confirm-inline** button ([§14.1](#141-buttons)): the first activation turns it into "Confirm 120" for 3000 ms, and the second buys. The money spent animates in the HUD ([§15.6](#156-gameplay-feedback)).
* **States:** unaffordable rows show the price in `red-1` and a disabled button ("Not enough money"). Max level shows a "MAXED" small stamp. Locked items show a lock icon and the requirement.

#### `staff`: **Personnel** (Phase 8)

* **Purpose:** hire, review and dismiss employees.
* **Layout:** List/detail. Toolbar tabs: **Staff / Applicants**. Rows are 36 lpx: 32×32 portrait thumbnail, name, role line, and morale LED.
* **Detail:** a **personnel file** (manila folder face `putty-4` with a tab at the top-left showing the name): portrait 64×64, stats as 5-segment meters (skill, reliability, morale), salary per day, hired day, a short generated note. Actions: Staff → **[Dismiss]** danger (always confirms: "Dismiss {name}? This will be noted in their file."). Applicants → **[Hire]** primary (shows the salary in the label: "Hire · 40/day").
* **Empty (Staff):** "Nobody works here but you. Lucky them." + [See applicants]

---

## 10. Phone interface

The phone is a **beige desk phone with an LCD strip, a red ring LED and big square keys**. Its screens are physical: the tray is the phone's top face, and the call console is the phone plus the company's caller-display add-on. It must never look like a chat app: no bubbles, no avatars in circles, no typing indicators with three bouncing dots.

### 10.1 Call flow states

```
idle ─► incoming (ringing) ─► active ─► ended ─► report
   └──► outgoing (dialling) ─┘     └─► (dropped / hung up / they hung up)
incoming ─► declined | missed ─► (toast) 
```

### 10.2 Incoming call: phone tray

Shown in **every** location and over the computer. It does not pause the game.

| Part | Rect | Spec |
|---|---|---|
| Tray | 332–475, 148–265 (144×118) | Putty-plastic panel (`putty-4`, outline, bevels) + 2 lpx dithered shadow. Slides up from below in 3 steps over 150 ms. |
| Header | 336–471, 150–161 | `text-label` "LINE 1" in `ink-3` + **ring LED** 7×7 at (463, 152): `red-2` blinking 300 on / 300 off while ringing, `ink-3` off |
| LCD | 338–469, 164–201 (132×38) | Sunken frame (1 lpx) around 3 rows × 12 lpx, 4 lpx side padding, `text-label` on LCD colours, 18 chars per row. Row 1: "INCOMING CALL" (blinks with the LED; steady in reduced motion). Row 2: caller display name or "UNKNOWN CALLER" (truncated). Row 3: in-world caller ID line (fictional number, or relationship "REGULAR · 3 CALLS", or the reason if known). |
| Thumbnail | 338–371, 204–237 | 32×32 portrait crop in a 1 lpx frame on the role colour. Unknown callers use `portrait-unknown` (silhouette). |
| Rings left | 376–469, 206–233 | `text-label` "RINGS" (row 1) + 5 pips 5×5 (`paper` remaining, `ink-3` used) on an `ink-1` well (row 2), one pip per ring burst |
| Answer | 338–401, 240–259 (64×20) | **Go** button, large, label "Answer" |
| Decline | 406–469, 240–259 (64×20) | **Danger** button, large, label "Decline" |

* **Ring pattern:** a 2000 ms ring burst (shake + LED + ring SFX), then 2000 ms of silence, repeated. Default 5 rings (rules decide the actual number). On the last ring the tray slides out (100 ms) and a "Missed call from {name}" info toast appears.
* **Shake:** the whole tray moves ±1 lpx horizontally on a 2-frame loop (50 ms each) during bursts only. Reduced motion: no shake, and the LED stays on.
* **Focus:** the tray **does not steal focus**. Its arrival is announced through an `aria-live="assertive"` region ("Incoming call from {name}"). `P` moves focus to Answer when no text field is focused. With focus in the tray, `Enter` answers and `Esc` does not decline (declining is a deliberate action).
* **Outgoing (dialling):** the same tray. LCD row 1 reads "DIALLING…", then "RINGING…". The pips count ringback tones. The only button is [Cancel] (secondary).

### 10.3 Answer transition

Answer → the tray's LCD shows "CONNECTED" for 150 ms, the handset-lift SFX plays, then the **call console** opens. The backdrop dithers in (2 steps, 100 ms) and the console slides up 3 steps (150 ms).

### 10.4 Active call: call console

Full screen layer over a 50% dithered backdrop. The HUD stays visible above it.

```
 4                                                                        475
 ┌──────────────────────────────────────────────────────────────────────────┐ 24
 │ ┌────────┐ ┌03:12┐ │ ┌─ LINE DEGRADED — SCRIPTED REPLIES (when needed) ┐│
 │ │portrait│ ▂▄▆█ OK │ │o  MAVIS  Hello? Who is this?                   o││
 │ │ 64×64  │ ▮▮▮▮▯▯▯▯│ │o  YOU    Good morning! I'm calling about...    o││
 │ └────────┘ ◉ MIC   │ │o  MAVIS  ▒ ▒ ▒                                 o││
 │ Mavis Thornbury    │ │o              (teleprinter tape, newest at     o││
 │ Retired, Lowmoor   │ │o               the bottom)                     o││
 │ Known since day 2  │ └────────────────────────────────────────────────┘│
 │ TRUST    ▮▮▮▮▒▒▯▯▯▯ │ [MIC] [ type a reply…                    ] [Send] │
 │ SUSPICION▮▮▯▯▯▯▯▯▯▯ │  Hold CTRL to talk · ENTER to send                │
 │ PATIENCE ▮▮▮▮▮▮▮▯▯▯ │                                                   │
 │ [      HANG UP     ] │                                                   │
 └──────────────────────────────────────────────────────────────────────────┘ 265
```

| Part | Rect | Spec |
|---|---|---|
| Console | 4–475, 24–265 | Putty-plastic panel, 2 lpx dithered shadow |
| **Portrait** | frame 12–79, 30–97 | Portrait frame 68×68 ([§14.11](#1411-portrait-frame-thumbnail-name-plate)) showing the live emotion; talk and blink loops |
| **Call timer** | 84–135, 30–45 | LCD box 52×16, `text-label` "03:12" (MM:SS; ≥ 60 min shows "59:59+") |
| **Line quality** | 84–135, 48–59 | 4 signal bars (3 lpx wide; heights 3/5/7/9; `paper` lit, `ink-3` unlit) + `text-label` "OK" / "NOISY" (`amber-2`) / "DOWN" (`red-2`) |
| **Audio visualiser** | 84–135, 62–73 | 8 bars × 3 lpx wide (2 lpx gap), max height 12, stepped every 100 ms from the playback level (caller) or the mic level (player, `hi-1` tint) |
| **Mic status** | 84–135, 78–89 | 5×5 LED + `text-label`: "MIC" off (`ink-3`), "LIVE" (`red-2`, while recording), "…" (transcribing, blinking), "MIC OFF" (setting off), "BLOCKED" (permission denied, `amber-2`) |
| **Name plate** | 12–135, 100–115 | `text-heading` display name, `ink-1` on `putty-5` |
| **Caller details** | 12–135, 118–171 | Up to 4 `text-ui` rows (14 lpx, ≤ 17 characters each, truncated with `…`): occupation, town, relationship + day known ("Regular · day 2"), facts known count |
| **Meters** | 12–135, 174–233 | 3 rows of 18 lpx: 9×9 icon + `text-label` (TRUST / SUSPICION / PATIENCE) above a **large** segmented meter 71×9; fuzzy display per game rules ([§14.9](#149-meters-and-progress-bars)). Rows hidden when the rules say the player cannot know them. |
| **Hang up** | 12–135, 240–259 | **Danger**, large, full column width, label "Hang up". Physically separated from Send. |
| **Transcript tape** | 144–471, 30–219 | [§10.5](#105-transcript-tape) |
| **Push-to-talk** | 144–163, 224–243 | 20×20 icon button (`icon-mic`). Hold = record. Shows mic state. Hidden when voice input is off. |
| **Reply input** | 168–423, 224–243 | Text input, large (20 tall), placeholder "Type a reply…", max 500 chars. When the transcription arrives it fills this field for editing. |
| **Send** | 428–471, 224–243 | **Primary**, large, "Send". Disabled when the input is empty or a reply is pending. |
| **Hint line** | 144–471, 248–259 | `text-ui` `ink-4` hint for the current mode ([§10.7](#107-voice-text-only-and-degraded-modes)) |

### 10.5 Transcript tape

* **Material:** `paper` strip with tractor-feed margins: 6 lpx `putty-5` margins left and right, with 2×2 `putty-3` holes every 8 lpx. The holes scroll with the text, which sells the "printout".
* **Text area:** x 154–461 (308 lpx = 44 chars), `text-body` (14 lpx lines), 13 visible lines.
* **Line format:** the speaker label is padded to 7 characters (`MAVIS  `, `YOU    `), with a hanging indent of 49 lpx (7 chars), so the text column is 37 chars wide. Caller labels use `carpet-2`, player labels `ink-4`, and text uses `ink-1`. Names longer than 6 chars are truncated in the label.
* **Newest at the bottom.** New lines feed in from the bottom: the tape scrolls up in 1-line steps (no smooth scroll). If the player has scrolled up, a 9×9 `↓` "new lines" button appears at the bottom-right of the tape.
* **Typewriter:** caller text reveals at the chosen text speed (default 40 chars/s; [§18.4](#184-text-options)). `Enter`/click on the tape completes it instantly. It is instant in reduced motion.
* **Waiting for a reply:** a pending line shows the caller label + "▒ ▒ ▒": three 5×5 dither blocks lighting in sequence (300 ms each), plus line crackle SFX. **No spinner.**
* **System lines** (call connected, line degraded, mic blocked): `text-label` in `ink-4`, centred, prefixed with `--`: "-- CALL CONNECTED 10:42 --".

### 10.6 Call completion: call report

When the call ends, the console slides down (100 ms), then a **CALL REPORT** opens: a medium modal (320 wide) on paper.

| Section | Content |
|---|---|
| Header | `text-title` "CALL REPORT", then a form-number line in `text-label` `ink-4`, ≤ 28 characters so it clears the stamp: "FORM 7B · DAY 03 · 10:42" |
| Who | 32×32 thumbnail + name (`text-ui`) + "Duration 03:12" |
| **Outcome** | Large stamp sprite (96×28) at the right of the header: AGREED (`green-1`), REFUSED / HUNG UP / LINE DROPPED (`red-1`), DECLINED / NO ANSWER / ENDED (`carpet-2`). The stamp lands after 300 ms: frame 1 is offset (-1, -1) with a 1 lpx `ink-0` shadow, frame 2 is settled, with the "thunk" SFX. |
| **Changes** | One row per visible attribute: icon + label + **1–3 arrow icons** (`icon-arrow-up`/`down`, 9×9, in the meter colour) + a plain-words bucket ("warmed up a lot", "a bit more wary", "running out of patience"). Exact numbers appear only if an upgrade reveals them (game rules). "No change" is shown as a 1 lpx dash. |
| **Noted** | Up to 3 new facts (`text-body`, prefixed "·") from the summary. Fallback summary: "Notes illegible." |
| **Performance** | Rules-generated lines only, e.g. "+1 toward quota", "+120" (money icon, `green-1`), "Strike: rude to caller" (`red-1`). |
| Action | **[File report]** primary, bottom-right. `Enter` activates. |

### 10.7 Voice, text-only and degraded modes

The same console serves every mode. The **hint line**, **mic LED** and **line quality** make the mode visible. Nothing changes position.

| Mode | Push-to-talk | Hint line | Mic LED | Line | Other |
|---|---|---|---|---|---|
| Voice in + out | shown | "Hold CTRL to talk · ENTER to send" | MIC / LIVE / … | OK | Visualiser active |
| Voice out only (input off) | hidden; input field grows to x 144 | "Type your reply · ENTER to send" | "MIC OFF" | OK | — |
| Mic blocked | shown **disabled**, tooltip "Microphone blocked. Allow it in your browser, or type instead." | "Microphone blocked — type instead" (`amber-2` icon) | "BLOCKED" | OK | System line on first attempt |
| Voice output off | as configured | as configured | as configured | OK | 9×9 `icon-speaker-off` beside the visualiser; visualiser shows the typewriter rhythm instead of audio |
| Browser voices (fallback TTS) | — | — | — | OK | No visual difference |
| **AI degraded / disabled** | as configured | as configured | as configured | **NOISY** (1 bar, `amber-2`) or **DOWN** when the circuit is open | A strip pinned to the top of the tape: `ink-1` bg, `amber-2` `icon-warning`, "LINE DEGRADED — SCRIPTED REPLIES". Shown whenever the last turn's `source` is `fallback` or `ai.enabled` is false. |
| Reply pending | disabled | "Waiting for {name}…" | — | — | Send disabled; the input stays editable |

**Honesty rule (MUST):** scripted/fallback replies are never labelled or styled as AI. The degraded strip is the label. Mock-provider replies in development show the same strip with "MOCK LINE — DEVELOPMENT".

### 10.8 Declined and missed calls

A declined call slides the tray out (100 ms) and raises a toast only if the rules attach a consequence ("Declined: {name}. Management noticed."). A missed call always raises an info toast.

---

## 11. Character art direction

### 11.1 Principles

* **Silhouette first.** Characters are told apart at 48 lpx tall and in a 32×32 thumbnail by hair shape, head shape, glasses/hats and one signature accessory, before colour.
* **Two-ramp colour signature.** Each character is defined by **one clothing ramp + one hair ramp** (plus skin). No two main characters share the same clothing + hair pair.
* **Personality through the face.** Portraits carry emotion. In-world sprites carry posture only.
* **Cheap to scale.** A new character is made from the **portrait kit** ([§11.5](#115-the-portrait-kit)). Expressions are drawn once per face base, not per character.

### 11.2 Clothing style

Late-80s/90s office and civilian wear: oversized blazers with shoulder pads, short-sleeve shirts with ties, cardigans, knitted vests, polo shirts, lanyards, pocket protectors, chunky glasses, perms, mullets, moustaches, scrunchies, windbreakers. Contacts (members of the public) dress as civilians: anoraks, fleeces, garden aprons, uniforms of fictional employers. **No real brands, logos, team kits or uniforms of real organisations.**

### 11.3 In-world sprites

Frame 32×56. The feet baseline is row 55. The body is centred on x 16.

| Body zone | Rows (from the top of the body, body top = frame row 8) | Width |
|---|---|---|
| Head (incl. hair base) | 0–11 | 10–12 |
| Eyes | rows 5–6, 1×2 lpx each, 3 lpx apart | — |
| Neck | 12 | 4 |
| Torso + arms | 13–30 | shoulders 16, arms hang to row 30 |
| Legs | 31–45 | 12 |
| Shoes | 46–47 | 12–14 |

* Facing: idle and seated face the camera (¾ toward the viewer); walk is side-on, right-facing, and flipped for left.
* Required animations (v1): boss `idle`, `walk`, `sit-talk`; coworkers `sit-type`, `idle`; player `seated-back` (2 frames, the back of the head and chair only).
* **The player is the camera.** The player character is never shown walking or standing. They appear only from behind: seated at their cubicle in `office-floor`, and in the visitor chair in `boss-office`. The player sprite is deliberately anonymous: grey cardigan (`ink-4/5`), brown hair (`wood-2`), no visible face.
* Coworkers in v1 are **recolours** of one seated base (`char-coworker-base`) with 3 hair shapes and 4 clothing ramps.

### 11.4 Portraits

64×64, transparent background (the frame supplies the role colour). Head and shoulders, front-facing (up to a 15° turn drawn into the art).

| Guide | Rows / columns |
|---|---|
| Top of skull | y 8 (hair may reach y 0) |
| **Brow line** | y 21–23 |
| **Eye line** | y 25–30 (eyes 4–6 lpx tall, 4–6 wide, pupils 2×2) |
| Nose | y 31–35 |
| **Mouth** | y 37–41 |
| Chin | y 46 |
| Shoulders | y 50 → exit the bottom edge |
| Head width | 26–30 lpx, centred on x 32 |
| **Thumbnail crop** | x 16–47, y 10–41 (must contain brows, both eyes and the mouth) |

Outline: 1 lpx `skin-edge` on the face and the darkest ramp step on clothes/hair. Lighting from the top-left, with the shadow side on the viewer's right. Eyes get one 1 lpx `paper` catchlight at the top-left of the pupil.

### 11.5 The portrait kit

Portraits are **assembled from layers** in Aseprite and exported flattened per character:

| Layer | Variants (v1) | Notes |
|---|---|---|
| `face` | 4 bases: round, long, square, heart × 4 skin ramps | The skin ramp is a palette swap within the master palette |
| `expression` | **6 emotions × 4 face bases**, each with `mouth-open` and `eyes-closed` overlays | Drawn once; aligned to the brow/eye/mouth guides |
| `hair` | free per character (front + back sub-layers) | Main identity carrier |
| `clothes` | collar/shoulder pieces | Clothing ramp |
| `accessory` | glasses, hat, earrings, pencil, headset, tie… | ≤ 2 per character |

A new character needs: hair, clothes, ≤ 2 accessories, and a choice of face base, skin ramp and clothing ramp. **Adding an emotion means drawing it for the 4 face bases** (instead of for every character), then re-exporting all characters.

### 11.6 Portrait backgrounds and role colours

The frame interior is a flat role colour with a 1 lpx `ink-0` inner ring. No patterns.

| Role | Background |
|---|---|
| `contact` | `carpet-1` |
| `coworker` | `wall-1` |
| `boss` | `red-0` |
| `candidate` | `wood-1` |
| unknown caller | `ink-2` + `portrait-unknown` silhouette (`ink-3` fill, `ink-4` rim light) |

### 11.7 Expressions

One frame per `Emotion` (ARCHITECTURE.md §7.5). These rules hold for every face base:

| Emotion | Brows (y 21–23) | Eyes (y 25–30) | Mouth (y 37–41) | Extra |
|---|---|---|---|---|
| `neutral` | level at y 22 | open, pupils centred | flat 4–6 lpx line | — |
| `happy` | raised 1 lpx, slight arch | lower lid raised 1 lpx (smiling eyes) | 6–8 lpx smile with corners up 1 lpx; may show a 1 lpx tooth row | 2 lpx cheek tone (`-2` skin step) |
| `confused` | one brow raised 2 lpx, the other level | pupils look up-left | small, offset 1 lpx, slanted | — |
| `suspicious` | lowered 1 lpx, angled inward | narrowed to 2 lpx, pupils to one side | pursed, 3 lpx | head tilt drawn in hair/shoulders (±1 lpx) |
| `annoyed` | flat, lowered 1 lpx | upper lid covers the top third | flat, one corner down 1 lpx | — |
| `angry` | sharp V, inner ends down 2 lpx | wide, pupils shrink to 1×2 | open, teeth row visible, corners down | 2×1 `red-1` flush on both cheeks |

Talk frames: `mouth-open` per emotion (open by 2 lpx). Blink: the `eyes-closed` overlay (a 1 lpx lid line) works with any emotion, and brows are unaffected.

Portrait sheet layout: **6 columns (emotions in `Emotion` enum order) × 3 rows (base, mouth-open, eyes-closed) = 384×192 px**.

### 11.8 Character identity checklist (per character)

1. Distinct silhouette in the 32×32 thumbnail against all existing characters (check side by side).
2. A unique clothing + hair ramp pair.
3. One signature accessory or shape, readable at 32×32.
4. Their portrait background matches their role.
5. 18 frames delivered (6 emotions × base/talk/blink).
6. Content validation passes (all emotion frames present, ARCHITECTURE.md §10.8).

---

## 12. Gameplay HUD

### 12.1 Layout (480×20, always on top)

`ink-1` bar with a 1 lpx `ink-0` bottom line (row 19) and a 1 lpx `ink-2` top highlight (row 0). Items are vertically centred: icons at y 5–13, text at y 4–15.

| Item | x | Content |
|---|---|---|
| Day phase icon | 4–12 | 9×9: briefing (clipboard), work (sun), review (stamp), evening (moon) |
| Day | 16–57 | `text-label` "DAY 03", `ink-6` |
| Clock | 62–96 | `text-label` "10:42", `paper`. Updates once per game minute. Blinks the colon 500/500 ms during work (static in reduced motion). Paused state: "PAUSED" in `amber-2` replaces the time. |
| Divider | 102 | 1×12 `ink-3` |
| Energy | icon 108–116, meter 119–159 | `icon-energy` + small segmented meter 41×9 |
| Sleepiness | icon 166–174, meter 177–217 | `icon-sleepiness` + small meter 41×9 + threshold tick at 70 |
| Coffee | icon 224–232, pips 235–245 | `icon-coffee` + 3 caffeine pips 3×5 (`wood-4` / `ink-2`); the icon shows a cooldown variant while the machine is unavailable |
| Divider | 252 | |
| Quota | icon 258–266, text 270–304, bar 307–346 | `icon-quota` + `text-label` "3/8" + continuous bar 40×5 (`paper` fill) |
| Divider | 352 | |
| Balance | icon 358–366, amount 369–431 | `icon-money` + right-aligned amount, 9 chars max ("1,234,567"), `green-2`, or `red-2` with a minus sign when negative |
| Memos | 438–453, y 2–17 | 16×16 icon button `icon-mail` + badge |
| Menu | 458–473, y 2–17 | 16×16 icon button `icon-menu` (opens pause) |

Every HUD item has a **tooltip with exact values and meaning**: "Energy 34/100 — drink coffee before it hits zero", "Sleepiness 72/100 — above 70 you may doze off", "Quota 3 of 8 deals — 4h 18m left", "Balance 1,240". Meters and numbers are announced to screen readers only on threshold changes, not every tick.

**Objectives** are not shown permanently. Hovering or focusing the Quota item shows a tooltip panel (up to 4 objectives as checklist rows). Completing one raises a success toast.

### 12.2 Energy feedback

| Energy | Meter | Icon | Other |
|---|---|---|---|
| 100–31 | `lime` segments | `icon-energy` | — |
| 30–11 | segments turn `amber` | `icon-energy-low` | One warning toast on crossing 30: "Running low. Coffee exists." |
| 10–1 | segments `red` | `icon-energy-low` blinks 500/500 ms | Warning toast on crossing 10; low heartbeat-like SFX once |
| 0 | empty | blinking | Sleepiness accelerates (rules). The HUD does nothing extra. |

Segments = `ceil(value / 10)`. Changes animate one segment per 50 ms ([§15.6](#156-gameplay-feedback)).

### 12.3 Sleepiness feedback

The meter is violet with a `hi-1` threshold tick between segments 7 and 8. Screen effects escalate (full spec in [§15.7](#157-screen-effects)):

| Sleepiness | Stage | Effect |
|---|---|---|
| 0–49 | Awake | None |
| 50–69 | Drowsy | Vignette level 1. The meter frame blinks once on entry. |
| 70–89 | Heavy | Vignette level 2 + slow blinks every 12–20 s. Warning toast "Your eyelids weigh a tonne." |
| 90–99 | Nodding | Vignette level 3 + blinks every 6–10 s. The meter frame outline turns `amber-2`. |
| 100 | Dozing | Eyelids close, the screen goes dark, a "Zzz" FX shows, the clock fast-forwards (the HUD clock ticks visibly), then wake-up or the boss-caught modal |

### 12.4 Coffee

* Making coffee happens in the world (coffee machine hotspot). The HUD shows the result: caffeine pips fill, energy segments refill one by one, sleepiness drains, and a floating "+ENERGY" label (`text-label`, `lime-2`) rises 8 lpx in 4 steps (50 ms each) above the energy meter.
* While the machine is cooling down or broken, the HUD coffee icon shows its `cooldown` frame and the tooltip gives the time left.

### 12.5 HUD visibility

| Context | HUD |
|---|---|
| Rooms, computer, phone, dialogue | Visible |
| Modals | Visible behind the backdrop, dimmed by it |
| Title, day card, game over, too-small notice | Hidden |
| Dozing | Visible above the effects layer |

---

## 13. Game screens and additional interfaces

All screens below are assembled from §14 components on the four materials.

### 13.1 Title screen

Full 480×270, no HUD. Background: the office exterior at night (`bg-title`, Phase 3 can use `bg-office-floor` with the evening overlay as a placeholder). The working title appears in `text-title` at (24, 40) until branding exists (D10). The menu is a vertical stack of large secondary buttons, 160 wide, at x 24, from y 120, with a 4 lpx gap: **Continue** (primary; only if a save exists), **New run**, **Settings**, **Credits** (Phase 9). Version string at the bottom-right, `text-ui` `ink-4`.

### 13.2 Day card

Full-screen `ink-0`. "DAY 04" in `text-title` `paper`, centred at y 110. Below it, a deadpan subtitle in `text-ui` `ink-6`, centred at y 146 (e.g. "Another day, another quota."). Hold 1200 ms, then the scene transition. Any key or click skips it after 300 ms.

### 13.3 Morning briefing

`boss-office` scene + **dialogue box** ([§14.12](#1412-dialogue-box)) with the boss portrait + a **Quota card** (clipboard: index card 168×120 at (304, 40), `putty-4` clip 24×6 at the top). The card shows "TODAY'S QUOTA", the target in `text-title`, and up to 3 objectives. The only action is **[Accept quota]** primary at the bottom-right of the card. The deadpan joke is that there is no other button.

### 13.4 Boss messages

* **Memos:** the boss memo style in Memos ([§9.6](#mail-memos-phase-4)).
* **Walk-by remark** (in the world): a small dialogue box variant (no input; 2 lines; auto-dismiss after 4000 ms or on click) with a boss portrait 32×32 thumbnail.
* **Toast:** "New memo from Management" (info, `icon-boss`).

### 13.5 Daily objectives

The checklist row component ([§14.15](#1415-lists-and-rows)) with a 9×9 checkbox sprite. It is used in Today, the briefing card and the HUD quota tooltip. Completed rows get `ink-4` text and a ticked box, never strikethrough.

### 13.6 End of day: performance review

A large modal (400×232) on paper: **PERFORMANCE REVIEW — DAY 03**.

| Area | Content |
|---|---|
| Left column (190) | Metrics table (`text-ui`, 14 lpx rows, zebra): Quota 5/6 · Calls 12 · Agreed 4 · Coffee 4 · Naps 1 · Strikes 1/3. A row that hurt the outcome has its value in `red-1`. |
| Right column (190) | "SUPERVISOR'S REMARKS" `text-label` + 4 lines of `text-body` (the boss's flavour line; AI may phrase it, scripted when degraded, with no AI claim either way) + 32×32 boss thumbnail. |
| Stamp | Large stamp at the top-right: PRAISED (`green-1`), PASSED (`carpet-2`), WARNED (`amber-0`), FIRED (`red-1`). Lands 300 ms after open. |
| Footer | Bonus/fine line with `icon-money`; **[Clock out]** primary, bottom-right. If fired, the button reads **[Collect your things]** and leads to game over. |

### 13.7 Evening and financial summary

The evening phase keeps the `desk` scene with the evening overlay and desk lamp. The computer shows the management apps. **Ledger → Day close** is the financial summary ([§9.6](#finances-ledger-phase-8)). The coat-rack hotspot "Go home" opens a small confirm modal: "Go home and end day 03?" [Stay] [Go home] (primary).

### 13.8 Random events: bulletin

A medium modal styled as a **notice pinned to a cork board**: `wood-3` 4 lpx cork border with 2×2 `wood-2` speckles, a paper card inside, a 5×5 `red-1` pin at the top centre. Content: 32×32 event icon (left), `text-title` title, `text-body` body (≤ 5 lines), up to 3 choice buttons stacked at the bottom-right (the first is primary only if it is the "default/safe" choice). Events without choices show **[Noted]**.

### 13.9 Office upgrades and employee management

These are Procurement and Personnel ([§9.6](#96-applications)). Physically visible upgrades (a new coffee machine, a plant) swap prop sprites in the world through `prop-*-<variant>` ([§20](#20-asset-naming-conventions)).

### 13.10 Caught asleep / strike

A small modal with a red 4 lpx top stripe: boss portrait frame 68×68 (angry) at the left, `text-title` "CAUGHT NAPPING", a `text-body` line, strike pips (3 × 9×9 `icon-strike`: `red-2` used, `ink-3` remaining). One button: [Get back to work] primary. The opening is preceded by a hard cut (no transition), the boss "ahem" SFX, and the eyelids snapping open (1 frame).

### 13.11 Game over: termination notice

Personality: the bureaucracy fires you with paperwork.

* **Transition:** fluorescent lights switch off section by section: three 150 ms steps darkening the scene with a 25 / 50 / 100% `ink-0` Bayer dither from left to right, with a "clunk" SFX per step. Then 300 ms of black, then the pink slip slides up in 4 steps (200 ms).
* **Layout (full screen, no HUD):** `ink-0` background. The **pink slip** (`rose-2` paper, 300×236, at (90, 17)) shows `text-title` "TERMINATION NOTICE", a form number, then fields as `text-label`/`text-ui` pairs: EMPLOYEE (the working identifier "You"), DAYS OF SERVICE, REASON (the deadpan cause from rules: "Asleep at desk during a client call."), CALLS MADE, COFFEES CONSUMED, MONEY MADE FOR THE COMPANY, BEST DAY. Big FIRED stamp at the bottom-right (lands after 400 ms). A 48×40 cardboard box sprite with a mug and a dead plant at the bottom-left of the slip ("personal effects").
* **Actions:** **[Start again from day 1]** primary, [Quit to title] secondary, bottom-right of the slip.
* Lifetime stats ("Best: day 7 · Runs: 4") in `text-ui` `ink-6` below the slip.

### 13.12 Restart

New run → the **DAY 01** card with the subtitle "Welcome aboard. Again." (first run: "Welcome aboard."). There is no other restart screen.

### 13.13 Pause menu

`Esc` (with no modal open) or the HUD menu button. A small modal (240 wide) styled as an **"ON BREAK" sign**: `text-title` "ON BREAK", a vertical stack of 4 large secondary buttons, 200 wide: **Resume** (primary), **Settings**, **Save and quit to title**, **Controls**. The game is paused. The HUD clock shows "PAUSED".

### 13.14 Settings

A large modal (400×232), titled "SETTINGS". Left: a vertical tab list (100 wide, 16 lpx tabs). Right: a form with labels above controls, 8 lpx between rows. Bottom-right: [Done] primary. Changes apply live and save to `uog:settings` on Done.

| Tab | Controls |
|---|---|
| Display | UI scale (stepper: Auto, ×2…×max), Fullscreen (toggle), Screen effects (Full / Reduced / Off), Reduced motion (toggle; defaults to the OS setting) |
| Audio | Master, Music, Sound effects, Voices, Interface (sliders 0–10) |
| Voice | Voice output (Cloud / Browser / Off), Voice input (Push-to-talk / Off), Push-to-talk mode (Hold / Toggle), Push-to-talk key (rebind), Subtitles (toggle, default on) |
| Text | Readable font (toggle), Text speed (Slow 20 / Normal 40 / Fast 80 chars/s / Instant) |
| Controls | Read-only key list + rebinding for push-to-talk and Show interactables |
| Save data | Export save, Import save, Delete save (danger, confirms) |

### 13.15 Save and load feedback

* **Saving:** bottom-left at (4, 256): 9×9 `icon-save` + `text-label` "SAVING" on an `ink-1` plate. Shown for at least 600 ms. It is replaced by `icon-check` + "SAVED" for 1200 ms, then disappears (2-step dither out).
* **Save failed:** a danger toast "Couldn't save. Your progress is at risk." with [Details] opening the save-problem modal (export option, retry).
* **Load problems** (title screen): a modal "SAVE DAMAGED" (corrupt/incompatible) with [Load backup], [Export file], [Start new run].

### 13.16 Too-small window

See [§17.3](#173-minimum-size-and-too-small-behaviour).

---

## 14. Reusable components

Every component lives in `client/ui/components/`, styled only with tokens, and appears in the component gallery. All sizes are lpx.

### 14.0 Frame construction (shared by all components)

| Frame | Construction | Used by |
|---|---|---|
| **Raised** | 1 lpx `--color-line` outline; inside it, 1 lpx bevel-light on the top + left and 1 lpx bevel-dark on the bottom + right; face fill | Buttons, windows, panels, phone, tabs |
| **Sunken** | 1 lpx outline; inside it, 1 lpx bevel-dark on the top + left and 1 lpx bevel-light on the bottom + right; face `paper` | Inputs, lists, LCD, wells |
| **Flat** | 1 lpx outline only | Index cards, paper documents, toasts, tooltips |
| **Floating** | Any frame + **hard dithered shadow**: 2 lpx band offset (2, 2) bottom-right, a 50% checkerboard of `ink-0` | Windows' child dialogs, modals, toasts, phone tray, tooltips |

CSS: the outline is `border: var(--border-1) solid var(--color-line)`. Bevels are **zero-blur inset `box-shadow`s** of 1 lpx (crisp, allowed). The floating shadow is a pseudo-element with `background: var(--dither-50-shadow)`. **No `border-radius`** anywhere. A "rounded" pixel corner is drawn by omitting one corner pixel, used only on folder tabs and the LED.

### 14.1 Buttons

| Property | Default (`md`) | Large (`lg`) | Small icon (`icon`) |
|---|---|---|---|
| Height | 16 | 20 | 16 × 16 |
| Padding x | 6 | 6 | — |
| Min width | 40 | 46 | 16 |
| Width rule | max(min width, 2 + 2×padding + 7 × characters [+ 13 with icon]) | same | — |
| Icon | 9×9, 4 lpx gap before the label | 9×9 | 9×9 centred (or 16×16 with no bevel padding) |
| Text | `text-ui`, centred, 1 line, no wrap | `text-ui` | aria-label + tooltip required |
| Frame | Raised | Raised | Raised |

Hit targets: 16 lpx minimum in both directions (≥ 32 CSS px at ×2).

| Variant | Face | Text | Bevel L / D | Use |
|---|---|---|---|---|
| `primary` | `carpet-2` | `paper` | `carpet-3` / `carpet-0` | The one main action of a group |
| `secondary` | `putty-4` | `ink-1` | `paper` / `putty-2` | Everything else |
| `danger` | `red-1` | `paper` | `red-2` / `red-0` | Destructive or irreversible |
| `go` | `green-1` | `paper` | `green-2` / `green-0` | Phone Answer only |
| `ghost` | transparent | `ink-1` (light) / `paper` (dark) + 1 lpx underline | none, no outline | Low-emphasis inline actions ("Open memo") |

| State | Change |
|---|---|
| Hover | Outline → `hi-1`. Primary face → `carpet-3`; secondary face → `putty-5`; danger/go faces unchanged. Cursor `interact`. |
| Pressed | Bevels swap (sunken look), label moves down 1 lpx, face → pressed token. Lasts while held, ≥ 50 ms. Click SFX on release. |
| Focus-visible | Focus ring ([§16.2](#162-focus)) |
| Disabled | No bevel (flat), face `putty-3`, text `ink-4`, 1 lpx outline `ink-4`, cursor `disabled`. Tooltip gives the reason. No hover change. |
| Loading (async action) | Label replaced by the 3-step dither "▒ ▒ ▒" in the label colour; width does not change; not clickable |
| Confirm-inline | First activation: label → "Confirm {amount}" with `hi-2` face and `ink-1` text for 3000 ms; a second activation commits; blur/timeout reverts |

Labels are verbs. "OK" is allowed only in pure acknowledgements, and "Noted" or "File report" are preferred there.

### 14.2 Panels

| Variant | Frame | Face | Padding | Title strip |
|---|---|---|---|---|
| `panel` (raised) | Raised | `putty-5` | 6 (compact 4) | Optional 14 lpx strip: `putty-4`, `text-label`, 1 lpx `putty-3` bottom line |
| `panel-sunken` | Sunken | `paper` | 4 | — |
| `panel-paper` | Flat | `paper` | 8 | Optional `text-heading` (or `text-title` for forms) + 1 lpx `ink-1` rule |
| `panel-dark` | Flat (`ink-0` outline) | `ink-1` | 4 | Optional `text-label` `ink-6` |

### 14.3 Windows

See [§9.3](#93-window-chrome). The component `Window` takes `title`, `icon`, `onClose`, `toolbar`, `status`, and `active`. Close is the only control.

### 14.4 Folder tabs

See [§9.2](#92-desktop-and-launcher). The component `FolderTab` takes `icon`, `title`, `active`, `badge`, and `locked`.

### 14.5 Index cards

Flat frame, `paper` face, header row 14 lpx (`text-heading`), a **1 lpx `red-1` rule** 2 lpx under the header, body padded 4/6. Optional 32×32 or 64×64 image on the left. Selected (in grids): 1 lpx `hi-1` outline + `hi-2` header background.

### 14.6 Modals

| Size | Width | Max height |
|---|---|---|
| small | 240 | 160 |
| medium | 320 | 200 |
| large | 400 | 232 |

* Raised + floating frame, `putty-5` face. Title strip 16 lpx (`text-heading` on `putty-4`, 1 lpx `putty-3` bottom line); form-style modals (call report, review, bulletin) instead open with a `text-title` header inside the body. Body padded 8. Footer: actions right-aligned, 4 lpx gap, 8 lpx from the edges.
* **Backdrop:** 50% checkerboard `ink-0` over everything below (`--dither-50`). No blur.
* Open: backdrop appears in 1 step; the panel slides up 4 lpx in 2 steps (100 ms). Close: 1 step (50 ms).
* Focus trapped. `Esc` closes **only** modals marked `dismissible`. Focus returns to the trigger. **One modal at a time.** A second request queues.
* Critical modals (caught asleep, fired review, save damaged) are not dismissible and pause the game.

### 14.7 Toasts

| Property | Value |
|---|---|
| Width | 160 |
| Height | 24 (1-line body) or 38 (2 lines); max 2 lines, the rest truncated |
| Frame | Flat + floating, `ink-1` face |
| Layout | 2 lpx **status stripe** on the left (status colour), 16×16 icon at (6, 4), title `text-label` at (26, 2), body `text-ui` `ink-6` at (26, 14) |
| Position | Top-right stack from (316, 24), newest on top, 2 lpx gap, **max 3** (older ones are dropped) |
| Duration | 3000 ms + 50 ms per body character, max 8000 ms; paused while hovered/focused; dismiss with a click or `Esc` when focused |
| Enter / exit | Slide in from the right 8 lpx in 3 steps (150 ms) / disappear in 2 dither steps (100 ms) |

| Type | Stripe + icon | Sound | Examples |
|---|---|---|---|
| info | `ink-6`, `icon-info` | soft tick | Missed call, new memo |
| success | `green-2`, `icon-success` | two-note chime | Objective done, money in |
| warning | `amber-2`, `icon-warning` | low double beep | Sleepiness rising, boss approaching |
| danger | `red-2`, `icon-danger` | buzzer | Strike, save failed |

Critical game events are **modals**, not toasts. The same message is never queued twice within 10 s.

### 14.8 Badges

Height 9, min width 9, padding x 2. `red-1` face, `paper` digits (`text-ui`; "9+" above 9), 1 lpx `ink-0` outline. Placed overlapping the icon's top-right corner by 3 lpx. No pulsing.

### 14.9 Meters and progress bars

**Segmented meter** (player and character meters):

| Size | Segment | Count | Total (with 1 lpx outline) |
|---|---|---|---|
| small (HUD) | 3×7, 1 lpx gap | 10 | 41 × 9 |
| large (call console, cards) | 6×7, 1 lpx gap | 10 | 71 × 9 |
| stat (Personnel) | 6×5, 1 lpx gap | 5 | 36 × 7 |

* Well: `ink-0` outline and `ink-0` gaps. Empty segment: `--color-meter-empty` (`ink-2`).
* Filled segment: rows 0–5 fill colour, the bottom row the shade colour (the stat size uses rows 0–3 + shade).
* **Threshold tick:** a 1 lpx `hi-1` column in the gap at the threshold, extending 1 lpx above and below the well (11 lpx tall).
* **Fuzzy:** the component takes `{ low, high }` in segments. Segments `≤ low` are filled, segments in `(low, high]` are **hatched** (a 50% checkerboard of the fill colour on `ink-2`), and the rest are empty. "Unknown" = all hatched + the tooltip "Unknown".
* **Change animation:** one segment per 50 ms toward the new value. A segment that changes flashes `paper` for 50 ms.
* **Accessibility:** `role="meter"`, `aria-valuenow`, and `aria-valuetext` ("Trust: somewhere between 40 and 60").
* Each meter is always accompanied by its **icon and label** (visible or in a tooltip). Colour alone never identifies it.

**Continuous bar** (progress, quota, research, call-cost): height 5 (1 outline + 3 fill + 1 outline), width as given, fill in 1 lpx steps, fill `--color-meter-progress` with a 1 lpx shade row, empty `ink-2`.

**Pips** (rings left, caffeine, level, strikes): 5×5 (caffeine 3×5) squares, 1 lpx gap, filled / empty colours as specified per use.

### 14.10 Status LEDs

5×5 with the corner pixels omitted (a "round" pixel LED) in a 1 lpx `ink-0` ring (7×7 total). States: off `ink-3`, on green `green-2`, amber `amber-2`, red `red-2`. Blinking: 300/300 ms (≤ 2 Hz). Always paired with a label or tooltip.

### 14.11 Portrait frame, thumbnail, name plate

| Part | Spec |
|---|---|
| Portrait frame | 68×68: raised frame (2 lpx) around the 64×64 portrait on the role background |
| Thumbnail frame | 34×34: 1 lpx `ink-0` outline around the 32×32 crop on the role background |
| Name plate | Width = frame width or more, height 14 (`text-ui`) or 16 (`text-heading`); `ink-1` face with `paper` text, or `putty-5` with `ink-1` in the call console; truncated with `…` |
| Emotion change | Instant frame swap (no transition), optional 1-frame "pop": the portrait shifts up 1 lpx for 50 ms |
| Degraded line | On a fallback reply, a 2-frame static overlay (`fx-static`, 64×64) plays once (100 ms) |

### 14.12 Dialogue box

For in-person conversations (boss, coworkers) outside the phone.

| Part | Rect | Spec |
|---|---|---|
| Box | 4–475, 194–265 | Raised + floating, `putty-5` |
| Portrait | 8–75, 196–263 | Portrait frame 68×68 |
| Name tab | from x 80, y 188–201 | A 14 lpx tab rising above the box edge, `ink-1` face, `paper` `text-ui` name |
| Text | 82–469, 198–239 | `text-body`, 3 lines, typewriter; a `▼` 5×5 "more" marker blinks at the bottom-right when there is more text |
| Input row | 82–469, 242–261 | Choice buttons (secondary, max 3, left to right) **or** the reply input + Send (same as the call console, large) |

### 14.13 Tabs

**Index tabs** (toolbars, settings): height 14, padding x 6, `text-ui`. Inactive: `putty-4` face, top + left + right outline, no bottom line. Active: `putty-5` face (joins the panel below), label `ink-1`, and a 1 lpx `hi-1` line along the top inner edge. Tabs overlap by 1 lpx. Keyboard: `←/→` move, `Enter` selects (roving tabindex).

### 14.14 Text input and text area

| Property | Value |
|---|---|
| Height | 16 (md) / 20 (lg); text area: rows × 14 + 4 |
| Frame | Sunken, `paper` face |
| Padding x | 4 |
| Text | `text-ui` `ink-1`; placeholder `ink-4` |
| Caret | 1×11 `ink-1`, blinks 500/500 ms (steady in reduced motion) |
| Selection | `hi-2` background, `ink-1` text |
| Focus | Focus ring ([§16.2](#162-focus)) |
| Error | Outline `red-1`; message under the field in `text-ui` `red-1` with `icon-danger` (2 lpx gap) |
| Disabled | Face `putty-5`, text `ink-5`, no caret |
| Counter | Optional right-aligned `text-ui` `ink-4` "123/500" under the field |

### 14.15 Lists and rows

| Row type | Height | Content |
|---|---|---|
| single | 14 | 1 line `text-ui` |
| double | 28 | line 1 `text-ui` + line 2 `text-ui` `ink-4` |
| portrait | 36 | 32×32 thumbnail (2 lpx padding) + 2 lines |
| checklist | 14 | 9×9 checkbox + text + right value |

* Padding x 4. Zebra striping only in tables. Lists use a 1 lpx `putty-3` divider.
* Hover: 1 lpx `hi-1` outline inset. Selected: `hi-2` background, `ink-1` text. Focus: focus ring inset.
* Keyboard: `↑/↓` move, `Home/End`, `Enter` opens, type-ahead by first letter.

### 14.16 State panels: loading, empty, error, locked

A centred block inside a content area, max 240 wide.

| Component | Icon | Text | Action |
|---|---|---|---|
| `StateLoading` | `icon-hourglass` (9×9, 4 frames × 200 ms), or 32×32 for full panels | 1 line `text-ui` in-world copy | — |
| `StateEmpty` | 32×32 relevant icon | `text-ui` line + optional `text-body` hint | Optional secondary button |
| `StateError` | `icon-danger` 9×9 or 32×32 | "Paper jam." + plain cause | [Try again] secondary |
| `StateLocked` | `icon-lock` | "Locked" + unlock condition | — |
| `StateDegraded` | `icon-warning` | Banner variant (full width strip, `ink-1`) | — |

**No spinners, no skeleton screens.** Loading under 300 ms shows nothing (avoids flashes). Over 1000 ms it adds a second line.

### 14.17 Checkbox, toggle, slider, stepper

| Control | Spec |
|---|---|
| Checkbox | 9×9 sunken box, `paper` face; checked shows a 5×5 `ink-1` tick sprite. Label to the right, 4 lpx gap. The hit area covers the label. |
| Toggle (rocker switch) | 22×11 raised; two halves "OFF/ON" (`text-label` would not fit, so a 5×5 `ink-1` dot marks the off side and a `green-2` 5×5 LED the on side); the pressed half is sunken. Label to the left. |
| Slider | Track 3 lpx sunken (`ink-2` face), length per layout (default 100). Thumb 7×11 raised `putty-4`. 11 discrete stops (0–10), with a 1×3 tick per stop below the track. Value shown at the right in `text-ui`. Keyboard `←/→` ±1, `PgUp/PgDn` ±2. |
| Stepper | [−] value [+]: two 16×16 icon buttons around a 40-wide sunken value field |

### 14.18 Scrollbars

7 lpx wide. Track: `putty-4` with a 50% `putty-3` dither. Thumb: raised `putty-5`, min height 16. No arrow buttons. Only shown when content overflows. (Firefox does not support custom scrollbar images: use `scrollbar-color: putty-5 putty-4` and `scrollbar-width: thin` there; this is an accepted difference.)

### 14.19 Tooltips

* Flat + floating frame, `ink-1` face, `paper` `text-ui` (1 line) or `text-body` (≤ 3 lines), padding 2/4, max width 160.
* Delay 400 ms on hover, immediate on keyboard focus, hidden on pointer leave/blur or `Esc`.
* Position: 8 lpx below-right of the pointer (hover) or 2 lpx below the element's bottom-left (focus), flipped to stay inside 4 lpx screen margins. Never covers its target.
* Format for world hotspots: **"Object — Verb"**. For meters: "Name value/100 — meaning".

### 14.20 Icons

* **Sizes:** 9×9 (small), 16×16 (medium), 32×32 (large). Each size is **drawn separately**, never scaled.
* **Style:** 1 lpx outline in the darkest step of the icon's main ramp, top-left light, ≤ 4 colours per small icon, ≤ 8 per large. Front-facing objects (a mug, an envelope, a clipboard), not abstract glyphs.
* **Colour:** icons are full-colour sprites. On dark UI they need ≥ 3:1 against `ink-1`. No emoji, no icon fonts, no stock icon sets.
* **App icons:** 16×16 and 32×32 per app: Today = desk calendar page, Card File = card box, Memos = envelope, Ledger = adding-machine tape, Initiatives = lightbulb in a tray, Procurement = cardboard box, Personnel = manila folder with a photo clip. A 9×9 small variant is used in title bars.

### 14.21 Cursors

16×16 sprites with a 1 lpx `ink-0` outline and a `paper` fill. Hotspot positions in brackets:

| Cursor | Hotspot | When |
|---|---|---|
| `cursor-default` | (1, 1) | Default arrow |
| `cursor-interact` | (5, 1) | Pointing hand over hotspots and buttons |
| `cursor-exit-left` / `-right` / `-down` | arrow tip | Exit tabs and doorways |
| `cursor-text` | (7, 8) | Text inputs |
| `cursor-wait` | (7, 8) | Async world action in progress (static hourglass) |
| `cursor-disabled` | (7, 8) | Unavailable interactable |

### 14.22 Stamps

Pre-drawn sprites, large 96×28 and small 48×14, in `stamp-good` / `stamp-bad` / `stamp-neutral` colours, with a rough ink texture (3–5 missing pixels in the letters, a double border). Drawn **straight** (no rotation). Large stamps animate in per [§10.6](#106-call-completion-call-report). Small ones are static.

### 14.23 LCD display

Sunken frame, LCD colours, padding 2/4, rows of 12 lpx in `text-label`. Unused character cells may show `wall-3` "ghost" blocks (a 5×7 block per empty cell) only on the phone tray. Blinking LCD text: 500/500 ms.

### 14.24 Key hints

Shows a shortcut: a "keycap" 11 lpx tall, min 11 wide, raised `putty-4` mini frame with a `text-label` key name ("ENTER", "ESC", "CTRL", "H"). Used in hint lines and the Controls tab.

### 14.25 Navigation elements

* **Exit tabs** and **location label** ([§8.5](#85-exits-and-navigation)).
* **Folder tabs** ([§9.2](#92-desktop-and-launcher)) and **index tabs** ([§14.13](#1413-tabs)).
* **Back** inside an app (detail → list on narrow content) is a ghost button "‹ Back" at the top-left of the content. v1 layouts are wide enough that it is not needed.

---

## 15. Animation guidelines

### 15.1 Rules

* **Stepped, not eased.** Use CSS `steps()` or frame lists. No `ease`, `cubic-bezier`, springs or bounces.
* **Short:** UI transitions 50–200 ms. Scene transitions 300 ms. Dramatic moments (game over) ≤ 1500 ms and skippable.
* **Opening is slower than closing** (150 vs 100 ms).
* **Translation only**, in whole lpx. No scaling, rotation, blur or opacity fades on sprites. Fades are dither steps.
* **Flashing ≤ 3 per second**, always.
* Animate with `transform: translate()` in lpx multiples and `steps()`. Never animate layout properties (`width`, `top`).
* Every animation has a **reduced-motion variant** (below). Reduced motion keeps state changes visible but static.

### 15.2 Interaction feedback

| Interaction | Animation | Reduced motion |
|---|---|---|
| Hover | Instant outline change (0 ms) | Same |
| Press | Bevel swap + 1 lpx label shift, ≥ 50 ms | Same |
| Toggle | Instant swap + click | Same |
| Tooltip | Appears in 1 step after the delay | Same |

### 15.3 Windows, panels, modals, toasts

| Element | Open | Close |
|---|---|---|
| App window (switching apps) | Content swaps instantly; title bar hatch "wipes" left→right in 3 steps (150 ms) | Instant |
| Child dialog / modal | Backdrop 1 step; panel up 4 lpx in 2 steps (100 ms) | 1 step (50 ms) |
| Phone tray | Up from below, 3 steps (150 ms) | Down, 2 steps (100 ms) |
| Call console | Backdrop 2 dither steps (25 → 50%) + panel up in 3 steps (150 ms) | Down, 2 steps (100 ms) |
| Toast | From the right 8 lpx, 3 steps (150 ms) | 2 dither steps (100 ms) |
| Reduced motion | All of the above appear/disappear in 1 step | |

### 15.4 Scene and screen transitions

| Transition | Spec |
|---|---|
| **Location change** | "Blink": the world dithers to `ink-0` in 3 steps (25 / 50 / 100% Bayer, 50 ms each), the new scene loads, then dithers in over 3 steps. 300 ms total. The HUD is unaffected. Reduced motion: hard cut. |
| **Computer on** | CRT power-on inside the screen rect: 1) black with a 1 lpx `paper` horizontal line at the vertical centre (50 ms); 2) a band ⅓ of the screen tall in `carpet-3` (50 ms); 3) the full desktop (50 ms). The bezel is drawn immediately. |
| **Computer off** | 1) a 1 lpx line (50 ms); 2) a 3×3 dot at the centre (50 ms); 3) back to `desk`. |
| **Day card** | Hard cut in, 1200 ms hold, dither out 3 steps. |
| **Game over** | [§13.11](#1311-game-over-termination-notice) |

### 15.5 Characters and props

* Idle and walk per [§4.5](#45-animation-frame-conventions). Walking speed is 40 lpx/s (2 lpx per 50 ms tick). Walk-bys cross `office-floor` in about 12 s.
* Characters never tween. Position changes snap to whole lpx each tick.
* Props loop independently. Interactable props may play an **activation animation** (coffee machine brew: 4 frames × 150 ms × 2 loops) that is cosmetic and never blocks commands (ARCHITECTURE.md §4.4).

### 15.6 Gameplay feedback

| Event | Feedback |
|---|---|
| **Phone ringing** | Tray shake (2 frames, 50 ms) + LED 300/300 + the desk-scene phone sprite alternates `ringing-a/b` (100 ms) + ring SFX, during 2000 ms bursts |
| **Incoming call** | Tray slides in (150 ms) on the first ring |
| **Coffee consumed** | Machine brew animation → mug SFX → HUD caffeine pips fill (1 per 50 ms) → energy segments refill (1 per 50 ms) → sleepiness segments drain (1 per 50 ms) → "+ENERGY" label rises 8 lpx in 4 steps |
| **Energy change** | Segment-by-segment (50 ms), with the changed segment flashing `paper` |
| **Money change** | HUD amount counts in up to 6 steps (50 ms each) to the new value; a floating "+120" / "-40" (`text-label`, money colours) rises 8 lpx above the balance in 4 steps |
| **Objective complete** | Checkbox ticks (2 frames, 100 ms) + success toast |
| **Strike** | HUD flashes its outline `red-2` twice (200/200 ms) + buzzer |
| **Boss approaching** | Warning toast "Footsteps…" + footsteps SFX getting louder; the boss enters `office-floor` from the left |
| **Game over** | [§13.11](#1311-game-over-termination-notice) |

### 15.7 Screen effects

The effects layer (`--z-effects`) sits above screens and below modals. It never covers the HUD rows 0–19. Effects use **only** `ink-0` dither patterns. CSS opacity is not allowed.

| Effect | Spec | "Reduced" setting | "Off" setting |
|---|---|---|---|
| Vignette L1 (sleepiness 50–69) | 16 lpx border band, 25% Bayer `ink-0` | Same (static) | None |
| Vignette L2 (70–89) | 32 lpx band: outer 16 at 50%, inner 16 at 25% | Same | None |
| Vignette L3 (90–99) | 48 lpx band: 75 / 50 / 25% in 16 lpx rings | Same | None |
| Slow blink (L2/L3) | Eyelids: `ink-0` bars from the top and bottom close to 30% height in 3 steps (100 ms each), hold 150 ms, reopen in 3 steps | None | None |
| Dozing (100) | Eyelids close fully in 4 steps (400 ms); a "Zzz" FX at the centre (3 frames × 300 ms) | Instant close; static "Zzz" | Dark screen + "Zzz" (still needed to show the state) |
| Evening overlay | 25% Bayer over the world canvas only | Same | Same (it is lighting, not an effect) |
| Modal backdrop | 50% checkerboard | Same | Same |

---

## 16. Interaction states and input

### 16.1 State matrix

| Element | Default | Hover | Pressed | Focus-visible | Disabled | Selected | Loading | Error |
|---|---|---|---|---|---|---|---|---|
| Button | per variant | `hi-1` outline, face step | bevel swap, label +1 | focus ring | flat, `putty-3`, `ink-4` | — | dither label | — |
| World hotspot | none | `hi-1` silhouette outline + tooltip | outline `hi-2` 100 ms | outline + focus brackets | `ink-5` outline + reason | — | `cursor-wait` | — |
| Folder tab | `putty-4` | `hi-1` outline | sunken | focus ring | lock icon | joined, `putty-5`, label | — | — |
| Index tab | `putty-4` | `hi-1` outline | — | focus ring | `ink-5` text | `putty-5`, `hi-1` top line | — | — |
| List row | paper | inset `hi-1` outline | — | inset focus ring | `ink-5` text | `hi-2` bg | — | — |
| Input | sunken paper | `hi-1` outline | — | focus ring | `putty-5` face | — | — | `red-1` outline + message |
| Checkbox/toggle | — | `hi-1` outline | sunken | focus ring | flat, `ink-5` | ticked/on | — | — |
| Meter | — | tooltip | — | tooltip | — | — | — | — |
| Push-to-talk | raised `putty-4` | `hi-1` outline | **recording**: sunken, `red-2` LED, mic level | focus ring | flat + tooltip reason | — | transcribing: blinking LED | blocked: `amber-2` icon |

### 16.2 Focus

* **Focus ring:** 1 lpx `hi-1` immediately outside the element's outline, plus 1 lpx `ink-0` outside that. This double ring reads on both light and dark surfaces. Implement it with `outline` + `box-shadow` in lpx (no blur).
* Shown only for keyboard focus (`:focus-visible`).
* World hotspots: the canvas draws the focus brackets ([§8.4](#84-interactive-indicators)), and the transparent button's ring is hidden.
* **Focus order:** HUD → world hotspots (left to right, top to bottom) → exit tabs → screen layer contents. Opening a screen moves focus to its first meaningful control. Closing it returns focus to the trigger.

### 16.3 Keyboard map (defaults)

| Key | Action | Context |
|---|---|---|
| `Tab` / `Shift+Tab` | Move focus | Everywhere |
| `Enter` / `Space` | Activate | Focused control |
| `Esc` | Back out, first match wins: close the tooltip → close a dismissible modal → close a child dialog → leave the computer → open pause. Never declines or hangs up a call; non-dismissible modals (call report, reviews) ignore it. | Everywhere |
| `H` (hold) | Show interactables | World |
| `P` | Focus the phone tray (when ringing) | Not while typing |
| `Left Ctrl` (hold) | Push-to-talk (rebindable; toggle mode in settings) | Active call |
| `Enter` | Send reply | Call/dialogue input |
| `Ctrl+1…7` | Open app N | Computer |
| `←/→`, `↑/↓` | Move within tabs, lists, sliders | Composite widgets |
| `F11` / Settings | Fullscreen | Browser/OS |

Letter shortcuts never fire while a text field has focus. The PTT default is provisional until the Phase 6 playtest ([§24](#24-open-design-decisions)).

### 16.4 Pointer and touch

* Mouse is the primary pointer. Hover gives the outline, tooltip and cursor.
* **Touch (tablets):** tap activates directly. A **long-press (500 ms)** shows the tooltip and the outline without activating. Push-to-talk works as press-and-hold. Holding `H` has a touch equivalent: a 16×16 "eye" icon button appears in the HUD menu area on touch devices.
* Drag is not used anywhere in v1.

---

## 17. Responsive behaviour

### 17.1 Scaling algorithm

```
dpr     = window.devicePixelRatio
scale   = floor(min(innerWidth * dpr / 480, innerHeight * dpr / 270))   // device px per lpx
--px    = scale / dpr  (CSS px)                                          // may be fractional in CSS px, exact in device px
game    = 480×270 lpx, centred; remainder letterboxed with --color-bg-app
```

* Recomputed on `resize`, browser zoom and `dpr` change.
* The player may choose a **smaller** integer scale in settings (Display → UI scale), never a fractional one.
* **Fullscreen** (settings or F11) is recommended in the title-screen hint when the current scale is below ×3 device px on a DPR-1 screen.
* There is no layout reflow at different sizes. The game is the same 480×270 composition at every size. **This is not a responsive website**, and nothing switches to a "mobile layout".

### 17.2 Reference viewports

Approximate windowed-browser viewports (CSS px):

| Device | Viewport | DPR | Scale (device px) | Game size (CSS px) | Fill |
|---|---|---|---|---|---|
| 1080p monitor | 1920×955 | 1 | ×3 | 1440×810 | 75% w |
| 1080p laptop at 125% | 1536×730 | 1.25 | ×3 | 1152×648 | 75% w |
| 1366×768 laptop | 1366×650 | 1 | ×2 | 960×540 | 70% w |
| 13" MacBook Air | 1470×840 | 2 | ×6 | 1440×810 | 98% w |
| 14" MacBook Pro | 1512×860 | 2 | ×6 | 1440×810 | 95% w |
| 1440p monitor | 2560×1310 | 1 | ×4 | 1920×1080 | 75% w |
| iPad landscape | 1180×820 | 2 | ×4 | 960×540 | 81% w |
| 1080p fullscreen | 1920×1080 | 1 | ×4 | 1920×1080 | 100% |

### 17.3 Minimum size and too-small behaviour

* **Supported** when `scale ≥ 2` **and** `--px ≥ 1.5` CSS px. That is a viewport of at least **960×540 CSS px at DPR 1** and **720×405 CSS px at DPR 2**. Text is then at least 16.5 CSS px.
* **Primary target:** desktop/laptop, `--px ≥ 2` (≥ 960×540 at DPR 1).
* **Below the minimum:** the game **auto-pauses** and shows the too-small notice on the system layer (`--z-system`). It is drawn with plain tokens at `--px = 1` CSS px × the largest integer that fits (possibly ×1), centred on `ink-0`: "The office doesn't fit." / "Make the window bigger, zoom out, or go fullscreen." / [Go fullscreen] / [Play anyway] (secondary; continues at the current scale and remembers the choice for the session). The notice disappears automatically once the window is large enough.
* **Tablets:** landscape is supported. In portrait, if the size is still supported, a dismissible info toast suggests rotating. Otherwise the too-small notice shows.
* **Phones:** not supported in v1. They usually fall below the minimum and get the notice (ARCHITECTURE.md §21 U11).

---

## 18. Accessibility and readability

### 18.1 Contrast and colour

* Text contrast per [§5.5](#55-contrast-wcag-2x-measured): ≥ 4.5:1 for body text, ≥ 3:1 for meters, icons and focus.
* **Colour is never the only signal:** meters have icons and labels, status has icons, money has a sign, LEDs have labels, hatching marks uncertainty and disabled states.
* **Colour-vision check (Phase 3):** run every key screen through deuteranopia/protanopia/tritanopia simulation. Trust (teal) vs suspicion (amber) must stay distinguishable by **position and icon** even if the hues merge.

### 18.2 Keyboard and focus

Full keyboard parity ([§16.3](#163-keyboard-map-defaults)), a visible double focus ring, and hotspots exposed as labelled buttons (ARCHITECTURE.md §4.1).

### 18.3 Motion and effects

* **Reduced motion** (setting, defaulting to `prefers-reduced-motion`) applies every reduced variant in §15.
* **Screen effects** setting: Full / Reduced / Off ([§15.7](#157-screen-effects)). When Off, sleepiness is communicated through the HUD meter, toasts and SFX only.
* Nothing flashes more than 3 times per second.

### 18.4 Text options

* **Readable font**: switches running text (`text-body` and multi-line `text-ui`: transcripts, memos, descriptions, tooltips) to Atkinson Hyperlegible Next at 11 lpx with 16 lpx line height. Fixed-width layouts (HUD, tables, LCD, tape labels) stay in Departure Mono so they keep their alignment.
* **Text speed**: Slow 20 / Normal 40 / Fast 80 chars per second / Instant. A click or `Enter` always completes the current line.
* **UI scale**: integer steps only ([§17.1](#171-scaling-algorithm)).

### 18.5 Audio and captions

* **Subtitles on by default**, always with the speaker's name.
* Every sound with gameplay meaning has a visual equivalent: ring → tray + LED, boss approaching → toast, strike → HUD flash.
* Separate volume sliders: master, music, SFX, voices, interface.

### 18.6 Motor and timing

* Push-to-talk has a **Toggle** mode.
* No action needs a double-click or a drag.
* Hit targets are ≥ 16×16 lpx.
* (Phase 7 rules) consider a "generous timers" option that extends ring time and dozing grace.

### 18.7 Screen readers

* Live regions: one polite region (toasts, objective updates) and one assertive region (incoming call, caught asleep, fired).
* Meters use `role="meter"` with `aria-valuetext` in words. The transcript is a `role="log"`.
* Decorative world art is `aria-hidden`. Each location has an accessible description ("Break room. A coffee machine, a fridge and a notice board.").

---

## 19. Audio direction

* **Sonic palette:** lo-fi, slightly crunchy, office-appliance-inspired. Think dot-matrix printers, desk-phone chirps, fluorescent hum, beige-plastic clicks and soft FM-synth tones. Original sounds only.
* **Categories:** UI (short, < 200 ms, quiet), world (appliances, footsteps, the phone), alerts (toasts by type, boss approaching, strike), voice (characters, through the phone-line filter on calls), music.
* **Music:** understated loops: muzak-gone-wrong for the workday (`music-office-day`), something sparser and later-in-the-evening for management (`music-office-evening`), and a short sting for game over. No vocals.
* **Variation:** repeated actions use 2–3 variants (`-01`, `-02`, `-03`).
* **Mixing (relative peak levels at 100% bus volume):** phone ring and danger alerts 0 dB (the loudest SFX); other alerts and world SFX −6 dB; voices −3 dB; UI −12 dB; music −15 dB; room tone (fluorescent hum) −24 dB. Ducking: music −6 dB while a call is active.

Starter SFX list (Phase 3–7):

| Id | When |
|---|---|
| `sfx-ui-click-01..03` | Button release |
| `sfx-ui-toggle` | Toggles, checkboxes |
| `sfx-ui-tab` | Tab/folder switch |
| `sfx-ui-open`, `sfx-ui-close` | Modal/window open and close |
| `sfx-ui-error` | Validation error |
| `sfx-toast-info`, `-success`, `-warning`, `-danger` | Toasts |
| `sfx-crt-on`, `sfx-crt-off` | Computer |
| `sfx-phone-ring` | Ring burst (2000 ms) |
| `sfx-phone-pickup`, `sfx-phone-hangup`, `sfx-phone-dial`, `sfx-phone-ringback` | Calls |
| `sfx-line-crackle-01..03` | Waiting for a reply, degraded line |
| `sfx-stamp-thunk` | Stamps |
| `sfx-coffee-brew`, `sfx-coffee-pour`, `sfx-coffee-sip` | Coffee |
| `sfx-door`, `sfx-footsteps-01..03` | Navigation, boss |
| `sfx-boss-ahem` | Caught asleep |
| `sfx-lights-off-01..03` | Game over |
| `sfx-room-hum` (loop) | Fluorescent ambience |
| `sfx-typing` (loop) | Coworkers typing |

---

## 20. Asset naming conventions

* Files and ids are **kebab-case**: `<category>-<subject>[-<variant>][-<state>][-<size>]`.
* **Categories:** `bg`, `prop`, `char`, `portrait`, `ui`, `icon`, `cursor`, `stamp`, `fx`, `sfx`, `music`, `font`.
* Animations live as **tags inside a sheet** (`char-boss` sheet with tags `idle`, `walk`, `sit-talk`), not as separate files.
* Character assets use the character's content id: `portrait-<character-id>`, `char-<character-id>`. Ids never change (AGENTS.md §2.8).
* Icon sizes are suffixes: `icon-coffee-9`, `icon-app-contacts-16`, `icon-app-contacts-32`.
* Variants describe content, not order: `prop-poster-teamwork`, `prop-coffee-machine-deluxe`, not `poster-2`.
* States are adjectives: `-broken`, `-ringing`, `-off`.
* Sound variants are numbered: `sfx-ui-click-01`.

| Example | Meaning |
|---|---|
| `bg-office-floor`, `bg-office-floor-fg` | Room plate + foreground occluder |
| `prop-coffee-machine` (tags `idle`, `brew`, `cooldown`, `broken`) | Prop sheet |
| `prop-window-view` (tags `morning`, `midday`, `afternoon`, `late`) | Time-of-day frames |
| `char-coworker-base` | Recolourable coworker |
| `portrait-boss` | 6×3 portrait sheet |
| `icon-energy-low-9` | Small icon, low variant |
| `cursor-exit-left` | Cursor |
| `stamp-hung-up-96`, `stamp-hung-up-48` | Stamp sizes |
| `fx-steam` | FX sheet |

---

## 21. Asset export specifications

### 21.1 Source and export

| Item | Rule |
|---|---|
| Source | `art/<category>/<id>.aseprite` (layered, keep layers and tags). Aseprite is recommended; any tool is fine if it exports the same formats. |
| Palette | Load `art/palette/uog-master.gpl` (or `.hex`). Indexed colour mode in sources is recommended. |
| Export | `client/public/assets/<category>/<id>.png` + `<id>.json` (Aseprite JSON, **array** format, frame tags, durations in ms) |
| Scale | **1×**. Never export upscaled (except cursors, §21.4). |
| Colour | PNG, RGBA, alpha **0 or 255 only**, every opaque pixel on-palette. Strip `gAMA`, `cHRM`, `iCCP`, `sRGB` chunks (no colour management shifts). |
| Sheet layout | Fixed frame size, frames left→right in rows, **no trimming, no padding, no spacing**, so frame rects are `(col × w, row × h)`. Max 2048×2048. |
| Durations | Multiples of 50 ms ([§4.5](#45-animation-frame-conventions)) |
| Anchors | Per [§4.3](#43-sprite-dimensions-fixed); props that deviate set a `pivot` slice in Aseprite, exported in JSON |
| Hotspot masks | Irregular interactables can export an optional `<id>-hit.png` (1-bit alpha mask) for pixel-precise hit-testing |
| Licences | Third-party fonts/sounds: a licence file next to the asset and an entry in the credits |

### 21.2 Palette check

A Phase 3 script (`scripts/check-palette`) fails CI if any exported PNG has a colour not in `uog-master.hex`, or any alpha other than 0/255.

### 21.3 Fonts

`client/public/assets/fonts/`: `departure-mono.woff2`, `jersey-15.woff2`, `atkinson-hyperlegible-next-regular.woff2`, plus `LICENSES.md` with each font's name, author, licence (OFL 1.1), source URL and version. `font-display: block` (with a 1 s timeout handled by showing the title screen only after fonts load) to avoid fallback-font flashes.

### 21.4 Cursors

CSS cursors are not affected by `--px`, so each cursor is exported **pre-scaled with nearest-neighbour** at ×1–×6 (`cursor-interact@3x.png`) and selected by the current scale. Browsers cap cursor size at 128×128, and ×6 = 96×96 stays within it. This is the only pre-scaled export.

### 21.5 Initial asset specification sheet (Phase 3–7)

Status: all **needed** (none drawn yet).

| Id | Category | Size (lpx) | Frames / tags (durations ms) | Ramps | Anchor | Phase |
|---|---|---|---|---|---|---|
| `bg-desk` | bg | 480×270 | 1 | wall, wood, putty | 0,0 | 3 |
| `bg-office-floor` / `-fg` | bg | 480×270 | 1 | wall, carpet, putty | 0,0 | 3 |
| `bg-break-room` / `-fg` | bg | 480×270 | 1 | wall, carpet, putty | 0,0 | 3 |
| `bg-boss-office` / `-fg` | bg | 480×270 | 1 | wall, wood, red | 0,0 | 3 |
| `prop-monitor-desk` | prop | 208×156 | `idle` 2 (600,600) glow | putty, carpet | bottom-centre | 3 |
| `prop-desk-phone` | prop | 96×56 | `idle` 1, `ringing` 2 (100,100) | putty, wall, red | bottom-centre | 3 |
| `prop-mug` | prop | 16×16 | `empty`, `full`, `steam` 3 (150) | putty, wood | bottom-centre | 3 |
| `prop-in-tray` | prop | 40×20 | `empty`, `full` | ink, paper | bottom-centre | 4 |
| `prop-coat-rack` | prop | 20×56 | 1 | wood | bottom-centre | 7 |
| `prop-coffee-machine` | prop | 48×64 | `idle` 2 (300), `brew` 4 (150), `cooldown` 2 (300), `broken` 1 | ink, putty, red, green | bottom-centre | 3 |
| `prop-wall-clock` | prop | 16×16 | 12 hour frames | putty, ink | centre | 3 |
| `prop-window-view` | prop | 64×48 | `morning`, `midday`, `afternoon`, `late` | carpet, violet, putty | 0,0 | 3 |
| `prop-fluorescent-tube` | prop | 48×6 | `on`, `dim`, `off` | ink, putty | 0,0 | 3 |
| `prop-poster-*` (3) | prop | 40×52 | 1 | any | 0,0 | 3 |
| `prop-water-cooler` | prop | 20×44 | `idle` 3 (300) | carpet, ink | bottom-centre | 3 |
| `prop-fridge` | prop | 32×64 | 1 | putty, ink | bottom-centre | 3 |
| `prop-vending-machine` | prop | 40×68 | `idle` 2 (600) | red, ink | bottom-centre | 3 |
| `prop-notice-board` | prop | 56×40 | `empty`, `pinned` | wood, paper | 0,0 | 3 |
| `prop-plant-dead` | prop | 16×24 | 1 | wood, wall | bottom-centre | 3 |
| `char-boss` | char | 32×56 | `idle` 2 (600), `walk` 4 (150), `sit-talk` 2 (300) | per character | (16,55) | 3/7 |
| `char-coworker-base` | char | 32×56 | `sit-type` 2 (200), `idle` 2 (600) | recolourable | (16,55) | 3 |
| `char-player-seated-back` | char | 32×56 | `idle` 2 (600) | ink, wood | (16,55) | 3 |
| `portrait-boss` | portrait | 64×64 (sheet 384×192) | 6 emotions × base/talk/blink | per character | 0,0 | 5 |
| `portrait-unknown` | portrait | 64×64 | 1 | ink | 0,0 | 6 |
| `portrait-<contact-id>` (initial library) | portrait | 64×64 (sheet 384×192) | 18 | per character | 0,0 | 5 |
| `icon-*-9` (HUD/UI set: energy, energy-low, sleepiness, coffee, coffee-cooldown, quota, money, mail, menu, phone, mic, mic-off, speaker-off, check, cross, lock, arrow-up/down/left/right, close, info, success, warning, danger, trust, suspicion, patience, save, hourglass (4 frames), boss, strike, phase-briefing/work/review/evening, eye) | icon | 9×9 | 1 (hourglass 4 × 200) | any | — | 3–7 |
| `icon-app-<id>-16` / `-32` / `-9` (7 apps) | icon | 16, 32, 9 | 1 | any | — | 4 |
| `icon-state-*-32` (empty/error/lock per app) | icon | 32×32 | 1 | any | — | 4 |
| `cursor-*` (7) | cursor | 16×16 (+ ×2…×6 exports) | 1 | ink, paper | per §14.21 | 3 |
| `stamp-*-96` / `-48` (agreed, refused, hung-up, declined, no-answer, line-dropped, ended, praised, passed, warned, fired, completed, maxed, profit, loss) | stamp | 96×28 / 48×14 | 1 | green, red, carpet, amber | — | 5–8 |
| `fx-steam` | fx | 8×12 | 4 (150) | ink, paper | bottom-centre | 3 |
| `fx-zzz` | fx | 16×16 | 3 (300) | paper, violet | centre | 7 |
| `fx-static` | fx | 64×64 | 2 (50) | ink | 0,0 | 6 |
| `ui-box-personal-effects` | ui | 48×40 | 1 | wood, putty | — | 7 |

---

## 22. Future design expansion

| Adding… | Do this | Must not |
|---|---|---|
| **A location** | Follow [§8.2](#82-scene-layouts)/[§8.3](#83-scene-composition-rules): 480×270 plate + `-fg`, the three bands, ≤ 8 hotspots, one ambient loop, an exit tab position, a location label and accessible description | Scroll, add parallax, change perspective or lighting |
| **A computer app** | Use one of the three app templates, a folder tab, 9/16/32 icons, an in-world title, all five states, and an entry in [§9.6](#96-applications) | Restyle window chrome; add another launcher |
| **A character** | Portrait kit + checklist [§11.8](#118-character-identity-checklist-per-character); sprites only if physically present | New proportions, a new face guide, or a portrait without all 18 frames |
| **An emotion** | Draw it for the 4 face bases, re-export every portrait, update the `Emotion` enum, ARCHITECTURE.md §7.5 and §11.7 | Add it for only some characters |
| **A meter/attribute** | Pick an existing accent hue that is free of conflicting meaning, or reuse `progress`; add an icon and a label | Add a palette colour without the 64-cap process |
| **An upgrade with visible art** | A `prop-*-<variant>` swap with the same footprint and anchor | Move hotspots between variants |
| **A screen** | Assemble it from §14 on one of the four materials ([§2.2](#22-the-four-materials)) | Invent a new frame, shadow or font size |
| **A component** | Specify it in §14 (dimensions, spacing, colours, type, frame, states, interactions), then build it and add it to the gallery and the reference sheet | Build first, document later |
| **A colour** | Propose the swap in a change that updates §5, `tokens.css`, the `.gpl` and `.hex`, and the change log; re-run the palette check | Exceed 64 colours |
| **Seasonal/event dressing** | Prop variants on existing anchors; optional temporary poster | Global palette shifts |

**Change control:** any intentional design-system change updates this document, `tokens.css` and the reference sheet in the same change, plus an entry in [§25](#25-change-log).

---

## 23. Anti-patterns

These **MUST NOT** appear. Reviewers reject changes that introduce them.

* Generic SaaS/dashboard styling: rounded cards, `border-radius`, soft or blurred shadows, gradients used as visual fills, glassmorphism/blur, pastel "startup" palettes, pill buttons, Inter/Roboto/system-UI fonts in game UI.
* Stock icon libraries (Lucide, Heroicons, Material, Font Awesome), icon fonts, or emoji used as icons.
* Spinners, skeleton loaders, progress shimmer, toast spam, modal stacks.
* Chat-app phone UI: speech bubbles, circular avatars, bouncing typing dots.
* Smooth-scaled, rotated or blurred pixel art; mixed pixel densities; fonts at non-native sizes; synthesised bold; sub-pixel positions.
* Off-palette colours; raw hex outside `tokens.css` and palette files; per-asset lighting directions; inconsistent outlines.
* Eased, springy or bouncing UI motion; opacity fades on sprites; parallax.
* Copying the look of real operating systems, real apps or other games (layouts, logos, sounds, characters). In particular, no "start" button + taskbar + desktop icon grid composition.
* Fake controls: minimise/maximise boxes, resize grips, dead menus, locked features shown as working UI.
* Joke copy on critical controls that makes their function unclear.
* Saturated accents used as decoration.

---

## 24. Open design decisions

| # | Decision | Status / owner | Current position |
|---|---|---|---|
| D1 | Logical resolution | **Resolved (Phase 2):** 480×270 | §4.1 |
| D2 | Master palette and semantic mapping | **Resolved:** 64 colours | §5 |
| D3 | Fonts and type scale | **Resolved** (measured in Chromium) | §6 |
| D4 | Outline, light, shading, dithering | **Resolved** | §3 |
| D5 | Camera/perspective | **Resolved:** stage view + oblique close-up | §3.1 |
| D6 | Portrait size; idle/talk scope | **Resolved:** 64×64, 18 frames | §11 |
| D7 | Computer OS identity | **Resolved:** putty/paper, hatch title bars, folder tabs | §9 |
| D8 | Transitions and timing set | **Resolved:** 50 ms tick, stepped | §4.5, §15 |
| D9 | Sound palette and music | Direction resolved; production in Phase 9 | §19 |
| D10 | Game name, logo, branding | Open (ARCHITECTURE.md §21 U1) | Title screen shows the working identifier |
| D11 | Art/audio asset licence | Open, **before the first art commit** (ARCHITECTURE.md §21 U5) | MIT vs CC BY 4.0 |
| D12 | Font rendering in Firefox/WebKit (Chromium measured: Departure Mono 7.00 advance @ 11; Jersey 15 exact @ 27) and glyph coverage (`…`, `·`, `‹`) | Phase 3 font spike | If a glyph is missing, use an icon or ASCII (`...`, `-`, `<`) |
| D13 | Push-to-talk default key (Left Ctrl) | Phase 6 playtest | Rebindable + toggle mode |
| D14 | Caffeine "jitters" visual (shaky UI, typos) | Phase 7 (game design) | Not designed. If added: ±1 lpx HUD shake ≤ 2 Hz, reduced-motion off |
| D15 | Fuzzy meter reveal rules (what the player knows) | Phase 5/7 (game design) | The component supports exact, ranged and unknown |
| D16 | Multi-window (draggable) computer | Post-v1 | Maximised windows only; no fake controls |
| D17 | Portrait lip-sync to audio amplitude vs a simple loop | Phase 6 | Simple 150 ms loop while audio plays |
| D18 | Exact boss/coworker character designs | Phase 5 (content) | Must follow §11 |
| D19 | Title logotype | With D10 | Bitmap sprite, cap height 15–30 lpx |

---

## 25. Change log

| Date | Change |
|---|---|
| 2026-11-26 | Phase 1: principles and constraints. |
| 2026-11-26 | Phase 2: full design system. 480×270 resolution (replaces the 640×360 recommendation), 64-colour palette, Departure Mono + Jersey 15 typography (fonts measured for pixel-exact sizes), spacing/layout system, office, computer, phone, character, HUD, screens, components, motion, interaction, responsive, accessibility, audio, naming, export and expansion rules. Tokens in `client/styles/tokens.css`; palette in `art/palette/`; visual reference in `design/reference/`. |
