# DESIGN.md

> **Status:** Phase 1 — design **principles and constraints**. Concrete values (hex colours, fonts, exact sizes, mockups) are deliberately left for **Phase 2 (`/design`)**, which must expand this document rather than replace it.
>
> **Scope:** This is the visual and interaction source of truth. If code and this document disagree, the document wins until it is deliberately changed. Technical constraints referenced here are defined in [ARCHITECTURE.md](ARCHITECTURE.md).

The goal: **a screen built weeks from now, by a different person or agent, must look like it belongs to the original game.**

---

## Contents

1. [How to use this document](#1-how-to-use-this-document)
2. [Art direction](#2-art-direction)
3. [Pixel grid, resolution and scale](#3-pixel-grid-resolution-and-scale)
4. [Visual consistency rules](#4-visual-consistency-rules)
5. [Colour and typography](#5-colour-and-typography)
6. [Components](#6-components)
7. [Layout and interaction principles](#7-layout-and-interaction-principles)
8. [Animation and feedback](#8-animation-and-feedback)
9. [Assets: specifications, naming and pipeline](#9-assets-specifications-naming-and-pipeline)
10. [Accessibility](#10-accessibility)
11. [Anti-patterns](#11-anti-patterns)
12. [Phase 2 process](#12-phase-2-process)
13. [Open design decisions](#13-open-design-decisions)

---

## 1. How to use this document

* **Before any visual work**, read this file and the relevant parts of ARCHITECTURE.md (§4 Frontend, §5 Rendering).
* **Reuse before you create.** Use existing tokens and components. If something genuinely new is needed, extend this document first (or in the same change), then implement it.
* Rules marked **MUST** are non-negotiable. **SHOULD** rules may be broken only with a written reason in the change description.
* Values marked *(P2)* are decided in Phase 2. Until then, do not hard-code guesses that will later look "official".

---

## 2. Art direction

### 2.1 The feeling

A **dingy, fluorescent-lit back office stuck somewhere between the late 1980s and late 1990s**, run by people who are very proud of very bad ideas. Beige computer towers, CRT monitors with a faint glow, humming strip lights, carpet tiles with suspicious stains, a coffee machine that is clearly the most important employee, motivational posters with slogans that undercut themselves.

Tone is **dark comedy played straight**: the world takes itself seriously; the humour comes from absurd details, deadpan copy and the gap between corporate cheerfulness and the grim reality. Never mean-spirited towards the people on the other end of the phone; the joke is on the operation.

### 2.2 Visual pillars

| Pillar | In practice |
|---|---|
| **Readable first** | Silhouettes and interactive objects read at a glance at ×2 scale. Clutter is background detail, never competing with interactables. |
| **Limited, moody palette** | A restricted palette with desaturated institutional colours (beiges, greys, sickly greens, tired blues) and a small number of saturated accents reserved for meaning (alerts, interactables, money, danger). |
| **Retro-tech UI** | The computer and phone UIs feel like plausible, *original* late-80s/90s office software — chunky bevels, pixel fonts, simple icons — without copying any real operating system or product. |
| **Small, achievable assets** | Few rooms, reusable props, small character sprites, expressive portraits. Detail goes where the player looks most (desk, computer, phone, portraits). |
| **Expandable** | Every new prop, room or character can be produced by following the specs in §9 without redesigning anything. |

### 2.3 Initial asset scope (keep it achievable)

Phase 3–7 need only:

* 3–4 office locations (e.g. desk view, office floor, break room with coffee machine, boss's office).
* ~20–30 props (most static; a few with 2–4 frame idle animations).
* Character **portraits** (one per character, one frame per emotion — see §6.9) — the main character art investment.
* Small in-world **character sprites** for people physically present (boss, coworkers): idle and walk only.
* UI kit: frames/9-slices, buttons, icons (16×16 and 32×32), cursors, meters.
* A small SFX set and 2–3 music loops.

Everything else (office customisation variants, more rooms, more animations) is Phase 8–9 and follows the same rules.

---

## 3. Pixel grid, resolution and scale

* **MUST:** one logical pixel grid for the entire game — world, UI, portraits, icons and fonts.
* **Logical resolution:** recommended **640×360** *(P2 to confirm)*. Rationale in ARCHITECTURE.md §5.1: integer scaling to 720p/1080p/1440p/4K.
* **MUST:** integer scaling only. No asset is ever drawn at a non-integer scale, rotated by arbitrary angles, or resampled. Need a bigger version? Draw a bigger version.
* **MUST:** all art is authored at 1× logical size. No "high-res" sprites mixed with low-res ones ("mixels").
* **MUST:** DOM UI is sized in logical pixels via the `--px` scale variable (ARCHITECTURE.md §4.7) so DOM and canvas share the same grid. Borders, gaps and paddings are whole logical pixels.
* **Grid unit** *(P2)*: a base spacing unit (recommended 4 logical px) with all spacing as multiples. Tile size for environment art *(P2, recommended 16×16)*.
* **Character proportions** *(P2)*: one fixed height for adult in-world sprites (recommended 32–40 logical px tall) and one portrait size (recommended 64×64 or 48×48). All characters share the same head-to-body ratio and the same portrait framing (head and shoulders, eyes on the same line).

---

## 4. Visual consistency rules

These rules exist so different people's art and screens look like one game. Phase 2 fills in the values; the *existence* of a single value for each is decided now.

| Aspect | Rule |
|---|---|
| **Pixel scale** | One scale for everything (§3). No mixels, no smoothing, no sub-pixel positions. |
| **Outlines** | **One** outline convention for all sprites and portraits *(P2 chooses: e.g. dark coloured "selective" outlines derived from the local palette ramp vs. uniform near-black)*. Outlines are 1 logical px. UI frames follow their own single documented convention. |
| **Lighting** | **One** global key-light direction for all world art *(P2, recommended top-left, matching overhead fluorescent lights slightly left of camera)*. Same shading steps per material (e.g. 3 tones + highlight). No per-asset lighting inventions. |
| **Colour palette** | Every pixel of every asset comes from the master palette (§5.1). UI tokens are derived from the same palette. Off-palette colours fail review. |
| **Shading style** | Flat shading with hard-edged tone steps; dithering only in documented places (e.g. shadows, CRT glow, modal backdrops). No gradients, no anti-aliasing to arbitrary colours. |
| **Animation style** | Frame-based, low frame counts, held key poses (§8.4). Same timing vocabulary across all assets. |
| **Character proportions** | Single body template and portrait template (§3). Variation through silhouette, hair, clothing, colour and props — not through changing proportions. |
| **UI treatment** | One frame style, one button style family, one icon style (same grid size, outline and light direction as world art), one font family set. |
| **Perspective** | Environment art uses one consistent camera *(P2 chooses: e.g. front-facing ¾ "stage" view for rooms; straight-on close-up for desk and screens)*. |

---

## 5. Colour and typography

### 5.1 Colour process (Phase 2)

1. **Master palette.** Choose or author one palette of roughly 32–48 colours *(P2)*, organised into ramps (e.g. beige ramp, grey ramp, green ramp, skin ramps, accent ramps). Record it as hex values in this document and as a `.gpl`/`.ase` palette file in `art/palette/`.
2. **Semantic roles.** Map palette entries to roles; code and UI reference **roles**, never raw hex:

   | Role group | Tokens to define *(P2)* |
   |---|---|
   | Backgrounds | `--color-bg-app` (letterbox), `--color-bg-surface`, `--color-bg-surface-sunken`, `--color-bg-overlay` |
   | Primary | `--color-primary`, `--color-primary-hover`, `--color-primary-pressed`, `--color-on-primary` |
   | Secondary | `--color-secondary`, … same states |
   | Accent | `--color-accent` (interactable highlight), `--color-accent-money`, `--color-accent-boss` (authority/threat) |
   | Text | `--color-text`, `--color-text-muted`, `--color-text-inverse`, `--color-text-disabled` |
   | Status | `--color-status-success`, `--color-status-warning`, `--color-status-danger`, `--color-status-info` |
   | Meters | `--color-meter-energy`, `--color-meter-sleepiness`, `--color-meter-trust`, `--color-meter-suspicion`, `--color-meter-patience` |
   | Lines | `--color-border`, `--color-border-strong`, `--color-bevel-light`, `--color-bevel-dark`, `--color-focus` |
   | Computer OS | `--color-os-desktop`, `--color-os-titlebar`, `--color-os-titlebar-inactive`, CRT tint |

3. **Contrast check.** Text/background pairs meet WCAG AA (4.5:1 body, 3:1 large/pixel-bold). Status is never conveyed by colour alone (icon or label too).
4. **Publish** tokens in `client/styles/tokens.css` (CSS custom properties) and, if the canvas needs them, `shared/design/tokens.ts`. These are the **only** places raw colour values may appear in code.

### 5.2 Typography process (Phase 2)

* **Pixel UI font** *(P2)*: one pixel font family for UI chrome, labels, headings and in-world text. Requirements: licence permitting redistribution and modification (OFL or similar), good legibility of `0/O`, `1/l/I`, punctuation, currency symbol; a regular and (if available) bold weight; renders crisply only at integer multiples of its design size.
* **Readable body font** *(P2)*: long-form text (dialogue transcripts, mail bodies, character bios) may use a larger pixel font or a second, highly legible pixel font. An **accessibility setting** offers a clean non-pixel fallback font for long text (still sized on the grid).
* **Type scale** *(P2)*: a small, fixed set of sizes as multiples of the font's native size, e.g. `--font-size-caption`, `--font-size-body`, `--font-size-title`, `--font-size-display`. No ad-hoc sizes.
* **Rules:** left-aligned text by default; line length ≤ ~60 characters in panels; no all-caps for more than a few words (except deliberately in-world signage); numbers that change (money, timers) use tabular/monospaced digits.
* **Copy voice:** in-world UI copy is deadpan corporate (e.g. euphemistic labels), but **controls must stay unambiguous**. The joke can be in the flavour text; never make "Hang up" say something the player has to decode.

---

## 6. Components

Every interface is assembled from these components, implemented once in `client/ui/components/` and documented in a component gallery (a dev-only screen from Phase 3). Phase 2 provides the detailed spec (sizes, paddings, states, sprites) for each.

All interactive components **MUST** define these states: **default, hover, pressed/active, focus-visible, disabled** — and where relevant **selected, loading, error**.

### 6.1 Buttons

* Variants: **primary** (one per view/action group), **secondary**, **danger** (destructive or irreversible: hang up, fire employee), **ghost/text** (low-emphasis). Icon-only buttons need an accessible label and tooltip.
* Look: chunky bevelled pixel buttons drawn with the UI frame convention. Pressed state shifts content **down by 1 logical px** and inverts the bevel; no scale transforms.
* Minimum hit target: 16 logical px tall at ×2 (≥ 32 CSS px).
* Labels are verbs ("Answer", "Buy", "Hire"), not "OK" where avoidable.

### 6.2 Panels

* The basic container: a **9-slice frame** with a consistent border thickness, an inner padding from the spacing scale, an optional title strip.
* Variants: *raised* (default), *sunken* (lists, inputs, readouts), *paper* (in-world documents/memos).
* Panels never use soft shadows; depth comes from bevels and a hard 1-px drop shadow if used at all *(P2)*.

### 6.3 Windows (computer OS)

* Every computer app opens in the same **window chrome**: title bar (app icon 16×16 + title), window controls (close; minimise/maximise only if implemented), content area, optional toolbar and status bar.
* Active vs inactive title bar colours. One window style — apps do not restyle chrome.
* Window open/close transitions per §8.3. Dragging/resizing only if implemented; no fake controls.

### 6.4 Computer applications (app template)

* Standard regions: **header** (app title/section, key action), **toolbar** (filters, search), **content** (list/detail, table, form), **status bar** (counts, last updated in game time).
* Required states, using shared components: **loading** (in-world: e.g. "Querying mainframe…" with a pixel hourglass), **empty** (in-world flavour text + what to do next), **error** (in-world wording + retry), **locked** (unavailable app or feature, with how to unlock).
* Data tables: fixed row height, zebra striping via tokens, sortable column header affordance consistent across apps.
* Each app may have **one** accent illustration/icon motif; layout and components are shared.

### 6.5 Dialogue

* Dialogue box anchored at the bottom of the screen (or inside the phone UI during calls): **portrait** (left), **name plate**, **text area**, **choices/input** area.
* Text reveal: typewriter effect at a set speed *(P2)* with click/key to complete instantly; never blocks reading. Respects reduced-motion (instant).
* Player input: text field with send button; push-to-talk button when voice input is enabled; transcription appears in the field for editing.
* Speaker emotion drives the portrait frame (§6.9). AI text is plain text — no rich formatting.
* Waiting for a reply: in-world indicator (e.g. character "thinking" dots, line crackle) — never a generic spinner.

### 6.6 Notifications (toasts)

* Appear in one corner *(P2)*, stack max 3, newest on top, auto-dismiss after a duration based on length *(P2)*, pause on hover, dismissable.
* Types map to status tokens and icons: **info**, **success** (money in, objective done), **warning** (sleepiness rising, boss nearby), **danger** (strike received).
* Each type has a distinct short sound (§8.6). Critical game events (fired, caught asleep) are **modals**, not toasts.

### 6.7 Progress bars and meters

* **Segmented pixel bars** (discrete blocks) for player meters (energy, sleepiness) and character meters (trust, suspicion, patience). Continuous fills for loading/progress (research, call timer).
* Each meter has: icon, label (visible or accessible), segments, threshold markers where meaningful (e.g. sleepiness danger zone), and a pulse/flash state when crossing a threshold.
* Character meters show only what the player *should* know: exact values are hidden behind in-world uncertainty unless an upgrade reveals them *(game design, Phase 5/7)*. The component supports "fuzzy" display (ranges/labels).

### 6.8 Phone interface

* **Incoming call:** caller portrait or silhouette, caller name / caller-ID text, call reason if known, ringing animation (handset shake, light blink), **Answer** (primary) and **Decline** (danger) buttons, ring timeout.
* **In call:** portrait with live emotion, name, **call duration** timer, subtitle area, input area (push-to-talk + text), mute, **Hang up** (danger). Visual "line quality" indicator when the AI is degraded/offline.
* **Call summary:** duration, outcome, attribute changes revealed per game rules, notes.
* The phone is a physical object: its UI should look like a device (handset/desk-phone screen), not a web form.

### 6.9 Character portraits

* One fixed size and framing for all characters (§3).
* **One frame per emotion** in the shared `Emotion` set (ARCHITECTURE.md §7.5): `neutral`, `happy`, `confused`, `suspicious`, `annoyed`, `angry`. Adding an emotion means drawing it for **every** character.
* Optional 2–3 frame idle (blink) and talk (mouth) loops *(P2 decides if in scope for v1)*.
* Portraits sit inside a standard portrait frame component; background colour may encode role (contact/coworker/boss) using tokens.

### 6.10 Tooltips

* Appear after a short delay *(P2, ~400 ms)* on hover or immediately on keyboard focus; offset from pointer; never cover the hovered target; one line preferred, max ~3 lines.
* Same frame style as small panels. Used for icon-only buttons, hotspots (object name + action verb, e.g. "Coffee machine — Drink coffee"), meters (exact meaning).

### 6.11 Modals

* Centred panel over a **dithered dark backdrop** (no blur). Title, body, actions (primary right/left per P2 convention — be consistent).
* Focus is trapped; `Esc` closes only non-critical modals; the triggering element regains focus on close.
* Used for: confirmations of irreversible actions, critical events (fired, caught asleep), settings, save problems. Game pauses while modals marked `pausesGame` are open (ARCHITECTURE.md §4.3).

### 6.12 Other shared patterns

Phase 2 should also specify: text inputs, checkboxes/toggles, sliders (volume), tabs, lists with selection, scroll bars (custom pixel style), cursors (default, pointer/interact, wait, disabled), HUD layout (clock, day, money, meters, quota) and hotspot highlight style.

---

## 7. Layout and interaction principles

* **Game, not website.** No page scroll, no navbars, no footers, no cards-on-grey dashboards. Everything lives inside the fixed logical canvas area.
* **Clear hierarchy.** One primary focus per screen. The HUD is quiet; urgency is signalled by status colours, motion and sound only when it matters.
* **Consistent placement.** Close buttons, primary actions, HUD elements and toasts are always in the same places *(P2 defines)*.
* **Spacing and alignment.** Only spacing-scale values; align to the grid; consistent internal padding per component.
* **Interactive affordance.** Anything clickable in the world shows a hover/focus highlight, a cursor change and a tooltip. Nothing looks clickable that is not.
* **Feedback within 100 ms.** Every input gets immediate visual and/or audio feedback, even if the result (e.g. AI reply) takes longer.
* **Keyboard parity.** Everything doable with a mouse is doable with a keyboard (Tab order, Enter/Space activate, Esc backs out). Shortcuts are documented in settings.
* **Honest UI.** Disabled or locked features look disabled/locked and say why. No decorative controls that do nothing.

---

## 8. Animation and feedback

### 8.1 Hover

* Instant (≤ 80 ms) highlight change; no easing-heavy fades. World hotspots: outline or brightening per the single P2 convention.

### 8.2 Click / press

* Pressed state (1-px shift, bevel swap) + click sound. Actions that start something slow show an immediate in-world waiting state.

### 8.3 Transitions

* **Restrained and short:** 100–250 ms. Prefer **stepped** animation (CSS `steps()` / frame-based) over smooth easing so motion feels pixel-native.
* Location changes: one transition style *(P2: e.g. quick fade-to-black or pixel wipe)*.
* Windows/panels: open with a short stepped scale-from-titlebar or wipe *(P2)*; close faster than open.
* No bounces, springs, parallax gimmicks or long choreographed sequences in functional UI.

### 8.4 Character animation

* Low frame counts (idle 2–4 frames, walk 4–6, talk 2–3). Frame durations from a small timing set *(P2, e.g. multiples of 100 ms)*.
* Held key poses and snappy timing; no tweened rotation/scaling of sprites.

### 8.5 Environmental animation

* Subtle ambient loops that make the office feel alive: flickering fluorescent tube, CRT glow, steam from coffee, blinking phone light, clock hands. Each loop is slow and low-contrast; it must not draw attention away from interactables.
* Sleepiness effects (vignette, slow blink, dithered darkening) are the strongest screen effects and are reserved for that system.

### 8.6 Loading states

* Always in-world (hourglass cursor, "dialling…", modem-style progress, coffee machine gurgling). No generic spinners, no skeleton screens.
* Loading that exceeds ~1 s shows progress or a message; exceeding the timeout shows the error state with retry.

### 8.7 Audio feedback

* Categories: **UI** (clicks, hovers sparingly, toggles), **world** (coffee machine, footsteps, phone ring), **alerts** (toasts by type, boss approaching, strike), **voice** (characters, filtered on phone), **music**.
* Every primary action has a sound; repeated actions use 2–3 variations to avoid fatigue. UI sounds are short (< 200 ms) and quiet relative to world/alerts.
* Same sonic palette throughout *(P2)*: e.g. lo-fi, slightly crunchy, office-appliance-inspired synth sounds. Music: understated loops that fit an office (muzak-gone-wrong), with an evening/management variant.
* No information is conveyed by sound alone (visual equivalent always exists).

---

## 9. Assets: specifications, naming and pipeline

### 9.1 Specification sheet (Phase 2 completes)

Phase 2 produces a table listing every initial asset with: id, category, size (logical px), frame count and durations, palette ramp(s), anchor point, and status (needed/drafted/final).

### 9.2 Naming

* Files and ids: **kebab-case**, `<category>-<subject>[-<variant>][-<state>]`, e.g. `prop-coffee-machine`, `prop-coffee-machine-broken`, `char-boss-walk`, `portrait-<character-id>`, `ui-button-primary`, `icon-app-contacts-16`, `bg-break-room`, `sfx-phone-ring`, `music-office-day`.
* Categories: `bg`, `prop`, `char`, `portrait`, `ui`, `icon`, `cursor`, `fx`, `sfx`, `music`, `font`.
* Character-specific assets use the character's content id (ARCHITECTURE.md §11), which never changes.

### 9.3 Pipeline

* Source art lives in `art/` (Aseprite `.aseprite` recommended; layered sources are kept).
* Export to `client/public/assets/<category>/` as PNG sprite sheets + JSON frame data (Aseprite JSON format), at 1× logical size, indexed to the master palette.
* A palette check (Phase 3 script) rejects exported PNGs containing off-palette colours.
* Every asset must be **original**. No ripped, traced or lightly edited third-party art. AI-generated images are not shipped as final assets unless they are redrawn/cleaned to these specs and the licence situation is recorded; they may be used as private reference only.
* Licences and credits for any third-party fonts/sounds are recorded alongside the files and in the in-game credits.

---

## 10. Accessibility

* Keyboard parity and visible focus (`--color-focus`, consistent 1-px or 2-px outline style *(P2)*).
* Contrast per §5.1; status never colour-only.
* **Reduced motion** setting (and `prefers-reduced-motion`): disables typewriter, screen shake, flicker loops and non-essential transitions; sleepiness effects become static overlays.
* **Subtitles** for all voiced lines, on by default; speaker name always shown.
* **Text options:** UI scale (integer steps) and readable-font option for long text.
* **Audio options:** separate volume sliders (master, music, SFX, voice, UI).
* Hotspots exposed as labelled buttons (ARCHITECTURE.md §4.1).
* No flashing faster than 3 times per second.

---

## 11. Anti-patterns

These **MUST NOT** appear. Reviewers should reject changes that introduce them.

* Generic SaaS/dashboard styling: rounded cards on light grey, soft drop shadows, gradients, glassmorphism/blur, pastel "startup" palettes, pill buttons, Inter/Roboto/system-UI fonts for game UI.
* Stock icon libraries (Lucide, Heroicons, Material Icons, Font Awesome) or emoji used as icons.
* Utility-CSS default looks; arbitrary inline colours or pixel values not from tokens.
* Smooth-scaled, rotated or blurred pixel art; mixed pixel densities; sub-pixel sprite positions.
* Off-palette colours; per-asset lighting directions; inconsistent outline styles.
* Spinners and skeleton loaders; toast spam; modal stacks.
* Copying the look of real operating systems, real apps or the inspiration game (distinctive layouts, logos, sounds, characters).
* Fake or decorative controls that look functional but do nothing.
* Joke copy on critical controls that makes their function unclear.

---

## 12. Phase 2 process

Phase 2 uses Claude's `/design` workflow. The designer/agent **MUST** read this document and [ARCHITECTURE.md](ARCHITECTURE.md) (at least §1, §4, §5, §7.5, §8, §9.7, §10.2) and [AGENTS.md](AGENTS.md) before starting.

**Inputs:** this document; the screen list and component list below; the content boundaries in ARCHITECTURE.md §1.5.

**Steps:**

1. **Mood & references.** Write a short art-direction brief with references described in words (eras, materials, lighting, colour moods). Original references only — no copying the inspiration game.
2. **Grid decisions.** Confirm logical resolution, spacing unit, tile size, character sprite height, portrait size, icon sizes.
3. **Palette.** Produce the master palette and semantic role mapping (§5.1), with contrast checks.
4. **Typography.** Select licensed pixel font(s), type scale, readable-font fallback.
5. **Conventions.** Decide outline style, light direction, shading steps, dithering usage, perspective, hotspot highlight style, transition styles, timing set.
6. **Components.** Full specs for every component in §6 with all states, sizes, paddings, and the sprites/9-slices they need.
7. **Screen layouts** at the logical resolution: title; office view + HUD; desk/computer desktop; app template (list/detail and table variants); phone incoming / in-call / summary; dialogue; morning briefing; end-of-day review; evening management hub; settings; pause; game over; save-error modal.
8. **Asset specification sheet** (§9.1) for Phase 3–7 needs.
9. **Sound direction** (§8.7) with a starter SFX list.
10. **Tokens.** Write values into this document and create `client/styles/tokens.css` (and `shared/design/tokens.ts` if needed).
11. **Self-check** against §4 and §11; update §13 (resolve or carry forward open decisions).

**Definition of done for Phase 2:** a Phase 3 developer can implement every listed screen and component without inventing a colour, size, font, spacing, transition or icon style. See also ARCHITECTURE.md §20 Phase 2 acceptance criteria.

---

## 13. Open design decisions

| # | Decision | Phase |
|---|---|---|
| D1 | Logical resolution (recommended 640×360) | 2 |
| D2 | Master palette and semantic mapping | 2 |
| D3 | Pixel font(s) and type scale | 2 |
| D4 | Outline convention, light direction, shading steps, dithering rules | 2 |
| D5 | Camera/perspective for rooms vs. close-ups | 2 |
| D6 | Portrait size, idle/talk animation scope | 2 |
| D7 | Computer OS visual identity (original, era-inspired) | 2 |
| D8 | Transition styles and timing set | 2 |
| D9 | Sound palette and music direction | 2 (direction), 9 (production) |
| D10 | Game name/logo/branding | Later (see ARCHITECTURE.md §21 U1) |
| D11 | Art asset licence | Before first art commit (ARCHITECTURE.md §21 U5) |
