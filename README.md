# untitled-office-game

> `untitled-office-game` is a **temporary working identifier**, not the game's name. Branding comes later.

A free, open-source, single-player **browser game**: a 2D pixel-art office simulation with dark comedy and absurd humour. You work a desk at a dubious, entirely fictional call-centre operation. You meet your boss's daily quota by talking to AI-powered characters on your office computer and phone, keep yourself awake with coffee, and try not to get fired. Get fired, and you start again from day one.

All organisations, people and scenarios in the game are fictional and satirical.

---

## Current status

**Phase 2 — Design: complete.** The repository contains architecture, design and engineering documentation, the design tokens (`client/styles/tokens.css`), the master palette (`art/palette/`) and a visual reference page (`design/reference/index.html`). **There is no game code yet**, so the game cannot be run.

| Phase | Focus | Status |
|---|---|---|
| 1 | Foundation: architecture, docs, configuration | ✅ Done |
| 2 | Design: visual identity, design system, layouts, asset specs | ✅ Done |
| 3 | First playable build: office, locations, navigation, core UI | ⏳ Next |
| 4 | In-game computer and modular apps | Planned |
| 5 | Characters and AI dialogue (OpenRouter) | Planned |
| 6 | Phone system: calls, microphone, text-to-speech | Planned |
| 7 | Mechanics: coffee, energy, sleepiness, boss, quotas, dismissal | Planned |
| 8 | Management and progression: finances, upgrades, staff, research, events | Planned |
| 9 | Polish and release | Planned |

Details, deliverables and acceptance criteria per phase: [ARCHITECTURE.md §20](ARCHITECTURE.md#20-development-roadmap).

## Project goals

* A cohesive, polished, hand-made-feeling game — not a pile of features.
* Conversations with distinct, consistent AI characters as the core gameplay.
* **The game always owns the rules**: AI provides dialogue and suggestions; deterministic game code decides outcomes.
* **Playable without AI, microphone or voice** — those are enhancements, not requirements.
* Cheap to run and easy to self-host. Minimal dependencies.

## Planned features

* Pixel-art office with clickable locations and interactive objects
* In-game computer with modular apps (dashboard, contacts, mail, finances, research, shop, staff)
* AI-driven characters with personalities, memory, trust, suspicion and patience
* Simulated phone calls: ringing, answer/decline, push-to-talk microphone input, AI voices, subtitles
* Coffee, energy and sleepiness — fall asleep and the boss may catch you
* Daily quotas, objectives and end-of-day performance reviews
* Finances, office upgrades, employees, research and random events
* Day-based progression with rising difficulty; restart from day one when fired

## Technology stack

| Area | Choice |
|---|---|
| Language | TypeScript |
| Client | Vite, Preact (UI), custom Canvas 2D renderer (pixel-art world) |
| Server | Node.js + Hono (stateless AI/voice gateway) |
| AI | OpenRouter — chat completions (structured JSON), text-to-speech, transcription |
| Validation | Zod (shared client/server) |
| Persistence | Browser `localStorage` with versioned saves |
| Testing | Vitest, Playwright |
| Deployment | Single Docker container; optional static offline build |

Reasoning for every choice: [ARCHITECTURE.md §2](ARCHITECTURE.md#2-technology-stack-and-reasoning).

## Repository guide

| File | Purpose |
|---|---|
| [ARCHITECTURE.md](ARCHITECTURE.md) | Technical source of truth: architecture, data models, AI/voice, saves, testing, roadmap |
| [DESIGN.md](DESIGN.md) | Visual and interaction source of truth: art direction, palette, type, components, screens, motion |
| [client/styles/tokens.css](client/styles/tokens.css) | Design tokens (the only place raw colours and font names live) |
| [art/palette/](art/palette/) | 64-colour master palette for art tools (`.gpl`, `.hex`) |
| [design/reference/index.html](design/reference/index.html) | Rendered visual reference: palette, type, components, greybox screen mock-ups |
| [AGENTS.md](AGENTS.md) | Mandatory rules for contributors and coding agents |
| [.env.example](.env.example) | All server configuration variables with safe defaults |

## Local development

### Prerequisites

* **Node.js 24 LTS or newer** (includes npm)
* Git
* Optional: an [OpenRouter](https://openrouter.ai) API key for AI dialogue and voices. Without one the game runs in offline mode with scripted fallback dialogue.
* Optional: Docker, for production-like runs.

### Setup

```bash
git clone <repository-url>
```

```bash
cd <repository-folder>
```

```bash
cp .env.example .env
```

Then edit `.env` (see below).

### Viewing the design reference

The reference page loads `client/styles/tokens.css` by relative path, so serve the repository root rather than opening the file directly:

```bash
python3 -m http.server 4517
```

Then open `http://localhost:4517/design/reference/index.html`. The page loads its fonts from public CDNs; the game itself will self-host them.

### Running the project

> **Not available yet.** These commands will exist from **Phase 3** (scaffolding). Until then there is no `package.json`.

| Command | Purpose |
|---|---|
| `npm install` | Install dependencies |
| `npm run dev` | Start client (Vite) and server with hot reload; open the URL Vite prints |
| `npm test` | Unit, integration and content tests |
| `npm run test:e2e` | Browser end-to-end tests (uses mock AI) |
| `npm run lint` / `npm run typecheck` | Static checks |
| `npm run build` then `npm start` | Production build and server |

## Environment configuration

The server reads configuration from environment variables (a `.env` file in development). Every variable is documented in [.env.example](.env.example) and [ARCHITECTURE.md §13](ARCHITECTURE.md#13-environment-configuration). The most important ones:

| Variable | Notes |
|---|---|
| `OPENROUTER_API_KEY` | Your OpenRouter key. **Server-side only — never put it in client code or a `VITE_*` variable.** Leave empty to run without AI. Setting a credit limit on the key is recommended. |
| `AI_PROVIDER` | `openrouter`, `mock` (offline scripted replies for development) or `disabled`. |
| `AI_DIALOGUE_MODEL` | Any OpenRouter model id that supports structured outputs. |
| `TTS_PROVIDER` / `TTS_MODEL` | Character voices. `browser` uses free built-in browser voices; `disabled` turns voices off. |
| `STT_PROVIDER` / `STT_MODEL` | Speech-to-text for push-to-talk. Typed input always works. |
| `DAILY_AI_REQUEST_BUDGET` | Set this on any public deployment to cap spending. |

`.env` is git-ignored. Never commit real keys.

## Contributing

Read [AGENTS.md](AGENTS.md) before contributing — it applies to humans and coding agents alike. Visual work must follow [DESIGN.md](DESIGN.md); architectural changes must follow (and update) [ARCHITECTURE.md](ARCHITECTURE.md).

## Licence

Source code is released under the [MIT Licence](LICENSE). The licence for art and audio assets will be confirmed before the first assets are added (see [ARCHITECTURE.md §21](ARCHITECTURE.md#21-unresolved-decisions)); until then, everything in this repository is MIT-licensed.

This project is an original work. It is inspired by the general concept of conversation-driven office games and does not use any assets, names, characters or branding from other games.
