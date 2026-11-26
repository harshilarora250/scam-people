# AGENTS.md

Permanent engineering instructions for **every** coding agent and human contributor working on `untitled-office-game` (working identifier — do not invent a game name).

These rules are mandatory. If a task seems to require breaking one, stop and say so instead of working around it. If a rule is wrong or outdated, change this file deliberately (in its own commit, with a reason) — do not silently ignore it.

---

## 0. Where things are

| Document | What it governs | Read it when |
|---|---|---|
| [ARCHITECTURE.md](ARCHITECTURE.md) | Stack, structure, data models, AI/voice design, saves, testing, roadmap | Before any change that touches structure, data shapes, state, server, AI, voice, saves or dependencies |
| [DESIGN.md](DESIGN.md) | Art direction, tokens, components, animation, assets | Before any change anyone can see or hear |
| [README.md](README.md) | Setup, status, licence | When setting up or changing setup/commands |
| This file | How to work | Always |

Current phase and what exists: see the **Implementation status** table at the end of ARCHITECTURE.md. **Do not assume code exists because a document describes it** — the documents describe the target; check the tree.

---

## 1. Project understanding (before touching anything)

1. **Read the relevant documents.** ARCHITECTURE.md before architectural changes; DESIGN.md before visual/audio changes. Read the specific sections, not just the headings.
2. **Inspect existing code before editing.** Search the repository (by name, by type, by command/event name) for the feature, helper, component or constant you are about to write. Never assume a feature does not exist.
3. **Map dependencies.** Identify which layers your change touches (see ARCHITECTURE.md §3.3 import rules): `shared/`, `client/engine`, `client/game`, `client/world`, `client/ui`, `client/controllers|services|voice`, `server/`. Check what imports the code you change.
4. **Know the phase.** Work belongs to the phase in ARCHITECTURE.md §20. Do not pull later-phase systems forward "while you're there" unless the task asks for it.
5. **Ask when the documents do not answer it.** If a decision is listed as unresolved (ARCHITECTURE.md §21, DESIGN.md §24), do not decide it implicitly in code. Raise it, or implement it behind a clearly named constant and flag it.

---

## 2. Architectural invariants (never break these)

1. **The simulation is pure.** `client/game/` imports nothing from the DOM, Preact, `fetch`, Web Audio, `localStorage`, `Date.now()` or `Math.random()`. Randomness uses the seeded RNG in state; time enters only via `tick` commands.
2. **Only command handlers and systems change game state.** UI, world, controllers and services call `dispatch(command)`. Nothing else mutates `GameState`. State is immutable — return new objects.
3. **AI proposes; rules decide.** AI output never writes to game state directly. It arrives as a validated `DialogueTurnAi` and is applied by `applyDialogueTurn` with clamping, multipliers and caps (ARCHITECTURE.md §11.4). Firing, reviews, quotas and money are **never** decided by AI.
4. **Secrets live only on the server.** API keys are read only in `server/config.ts`. Nothing secret goes into client code, `VITE_*` variables, logs, error messages, fixtures or commits.
5. **The server builds prompts.** The client sends IDs, bounded numbers, enums and the player's own words — never prompt text. Do not add a request field that lets the client influence system prompts.
6. **Everything external can fail.** Every network/provider/mic/audio call has a timeout, a typed error result and a fallback that keeps the game playable.
7. **Saved shapes are versioned.** Any change to the shape of saved state bumps `schemaVersion`, adds a migration, a fixture of the old version and a migration test (ARCHITECTURE.md §12.4).
8. **IDs are forever.** Content IDs (characters, upgrades, events, apps, locations) and storage keys (`uog:*`) are never renamed after they ship. Deprecate instead.
9. **Providers stay isolated.** OpenRouter/ElevenLabs specifics live only in `server/ai/*-llm.ts` and `server/voice/*-tts.ts|*-stt.ts`. Feature code depends on the `LlmProvider`, `TtsProvider`, `SttProvider` interfaces.
10. **Content is fictional.** No real companies, banks, agencies, brands, public figures or private individuals; no realistic, transferable fraud instructions; no collection of real personal data (ARCHITECTURE.md §1.5). This applies to code, content, prompts, fixtures and test data.

---

## 3. Engineering standards

### 3.1 Code quality

* TypeScript `strict`. No `any` (use `unknown` + narrowing); no `@ts-ignore` (use `@ts-expect-error` with a reason, sparingly); no non-null assertions on data from outside the module.
* Validate at boundaries with Zod: HTTP requests/responses, AI output, save files, content data. Inside a boundary, trust the types.
* Expected failures are returned as discriminated unions (`{ ok: true, … } | { ok: false, error }`), not thrown. Throw only for programmer errors.
* Pure functions wherever possible; side effects at the edges (controllers, services, server routes).
* Comments explain *why*, not *what*. No commented-out code.

### 3.2 Size and responsibility

* One responsibility per module. If you need "and" to describe a file, split it.
* Guidelines (not hard limits, but exceeding them needs a reason): files ≤ ~300 lines, Preact components ≤ ~150 lines, functions ≤ ~50 lines, ≤ 4 parameters (use an options object beyond that).
* Computer apps live in `client/ui/computer/apps/<app-id>/`. An app never imports another app's internals.
* Systems live in `client/game/systems/`, one system per file. Balance numbers live in `client/game/rules/`, never inline.

### 3.3 Naming conventions

| Thing | Convention | Example |
|---|---|---|
| Files and folders | kebab-case | `energy-system.ts`, `call-screen.tsx`, `call-screen.module.css` |
| Types, interfaces, components | PascalCase | `GameState`, `CallScreen` |
| Functions, variables | camelCase | `applyDialogueTurn`, `energyDecayPerMinute` |
| Constants (module-level, fixed) | SCREAMING_SNAKE_CASE | `LOGICAL_WIDTH` |
| Command / event `type` strings | camelCase verbs / past-tense facts | `drinkCoffee` (command), `coffeeConsumed` (event) |
| Content IDs, asset IDs, app IDs | kebab-case, stable | `break-room`, `prop-coffee-machine`, `contacts` |
| Env vars | SCREAMING_SNAKE_CASE, grouped by prefix | `AI_DIALOGUE_MODEL`, `TTS_MODEL` |
| Tests | `<module>.test.ts` next to the module; E2E in `tests/e2e/*.spec.ts` | `energy-system.test.ts` |

### 3.4 Reuse, abstraction and dependencies

* Reuse existing utilities, selectors, components and tokens. Duplicated logic is a defect: extract it to the right shared place (`shared/`, selectors, `ui/components/`).
* Do not create abstractions for a single use. Introduce an interface when there is a second implementation or a real test seam (the provider interfaces are deliberate).
* **Dependencies:** the runtime budget is `preact`, `hono`, `@hono/node-server`, `zod`. Adding *any* runtime dependency requires a decision-log entry in ARCHITECTURE.md §22 (what, why, size, alternatives). Dev dependencies need a one-line reason in the change description. Never add a dependency for something achievable in ~50 lines of clear code. Never add UI kits, CSS frameworks, icon libraries, state libraries or game engines.

---

## 4. Feature development

* **Incremental.** Small, reviewable changes that each leave the game working. Land the data shape + logic + tests before the polish.
* **Stay in scope.** Do not rewrite, reformat or "clean up" unrelated code. If you find a problem elsewhere, report it (or fix it in a separate change).
* **No fake functionality.** Never present placeholder data, stub responses or decorative controls as working features. If something is not implemented:
  * do not render it, **or** render it in the DESIGN.md *locked/disabled* state with an honest reason;
  * mark code with `// TODO(phase-N): <what is missing>` and list it in the Implementation status table.
* **Mocks are explicit.** `MockLlmProvider` and other mocks are selected only by configuration (`AI_PROVIDER=mock`) or tests, and the UI must never claim a mock reply is AI-generated in production.
* **Handle all states.** Every view that loads or depends on data handles **loading, empty, error** (and **offline/degraded** for AI/voice) using the shared components.
* **Interactions must actually work** — keyboard included. Verify by running the game, not by reading the code.
* **Content changes** (characters, scenarios, upgrades, events) must pass the content validation test and respect ARCHITECTURE.md §1.5 and §11.

---

## 5. Visual consistency

* **Read DESIGN.md before implementing anything visual or audible**: at least the sections you touch plus §4 (pixel-art specifications), §5 (palette), §14 (components) and §23 (anti-patterns). Use `design/reference/index.html` to see how components and screens should look.
* **DESIGN.md is the visual source of truth.** Do not invent new colours, fonts, sizes, spacing, shadows, radii, transitions or icon styles. Never introduce an arbitrary visual style "just for this screen". Use tokens (`client/styles/tokens.css`) and components (`client/ui/components/`).
* **Palette:** every colour comes from the 64-colour master palette (DESIGN.md §5), used through semantic `--color-*` tokens. Saturated accents keep their reserved meanings (§5.4). Raw colour values and font declarations appear **only** in token files (`tokens.css`, `art/palette/*`).
* **Pixel-art specifications:** 480×270 logical grid, integer scaling only, one pixel size (no mixels), fixed sprite and portrait sizes, one outline/lighting/shading convention, 50 ms animation tick (DESIGN.md §3–4, §11, §20–21). Raw pixel values in CSS must be multiples of the logical pixel (`calc(var(--px) * n)` or a `--sp-*`/`--size-*` token).
* **Spacing and typography:** only the `--sp-*` spacing scale and the five text styles (DESIGN.md §6–7). Fonts only at their native sizes: Departure Mono 11, Jersey 15 at 27. Never bold, never other sizes.
* **Reuse established components.** New screens are assembled from existing components on one of the four materials (DESIGN.md §2.2). If a new component is needed, specify it in DESIGN.md §14 first (same change is fine), then build it in `ui/components/`, add it to the component gallery and the reference sheet. Do not create a visually different version of an existing component.
* **Consistent treatment across applications:** every computer app uses the shared window chrome, one of the three app templates and the shared loading/empty/error/locked states (DESIGN.md §9). Apps never restyle chrome.
* Pixel art follows DESIGN.md §3–4 and §20–21: one scale, master palette only, one outline and lighting convention, kebab-case asset names, original work only.
* **Never** introduce generic AI-generated SaaS/dashboard styling (DESIGN.md §23): rounded cards, soft shadows, gradients, blur, stock icon sets, emoji icons, system fonts, spinners.
* **Intentional design-system changes update DESIGN.md**, `tokens.css` and the reference sheet in the same change, with a DESIGN.md §25 change-log entry. Code must never silently diverge from DESIGN.md.
* Before finishing visual work, compare it side by side with an existing screen at the same scale. If it does not look like the same game, it is not done.

---

## 6. AI and voice integration

* Never expose credentials; never log request bodies, prompts with player text, audio or transcripts in production.
* Keep provider code isolated behind interfaces (§2.9). Model IDs come from config, never from feature code.
* Validate every AI/voice response server-side with Zod; sanitise and clamp; return a valid fallback on any failure (ARCHITECTURE.md §7.6–7.7).
* Every AI/voice call: timeout, bounded retries for transient errors only, typed error kinds, circuit breaker respected.
* Respect token and length limits (ARCHITECTURE.md §7.8). Do not raise limits without updating the docs and checking cost.
* Prompt templates live in `server/ai/prompts/` as pure functions with snapshot tests. Changes to global rules or the output schema require re-running the AI eval (`npm run ai:eval`, opt-in, costs money — ask before running it) once it exists.
* Tests never call real providers. Use mocks and recorded fixtures.
* The game must stay fully playable with `AI_PROVIDER=disabled`, with no microphone, and with voice output off. Check these paths when you touch dialogue or calls.

---

## 7. Documentation

* Update docs **in the same change** when you alter architecture, data shapes, env variables, scripts, conventions or visual rules:
  * new/changed env var → `.env.example` + ARCHITECTURE.md §13 + README if it affects setup;
  * new runtime dependency or architectural choice → ARCHITECTURE.md §22 decision log;
  * phase progress → Implementation status table in ARCHITECTURE.md and the status section in README;
  * new component/visual rule → DESIGN.md (+ `tokens.css`, `design/reference/index.html`, change log §25).
* Documentation must describe reality. Never claim a feature, script or command exists before it does; label targets as "planned"/"target".
* Keep docs consistent with each other; if you find a contradiction, fix it or report it.

---

## 8. Agent workflow

### Before making changes

1. Read the relevant documentation sections (§0).
2. Inspect the relevant source files and their callers/tests.
3. State the intended change briefly (what, where, why) — in your plan or PR description.
4. Check dependencies and invariants (§2) the change could affect; check the phase scope.

### While making changes

* Make the smallest change that fully solves the task. Keep commits focused.
* Write or update tests alongside the code.

### After making changes

1. **Run the checks** (once the scripts exist — see README): `npm run typecheck`, `npm run lint`, `npm test`; `npm run test:e2e` when UI flows, routes or saves changed; `npm run build` before declaring done.
2. **Check for regressions:** run the game (`npm run dev`) and exercise the changed flow plus the core loop (start → navigate → computer → call → save/reload), including keyboard use and the AI-disabled path where relevant.
3. **Review your own diff** for: invariant violations, leftover debug code, hard-coded values that belong in rules/tokens/config, missing states, accessibility, docs drift.
4. **Update documentation** (§7).
5. **Report honestly:** what changed, what was tested and how, what was not verified, and any follow-ups or `TODO(phase-N)` items. If a check fails, say so with the output.

### Definition of done

- [ ] Behaviour works in the running game (not just in tests), including keyboard.
- [ ] Typecheck, lint, tests and build pass.
- [ ] Loading/empty/error/offline states handled where applicable.
- [ ] Visuals use only DESIGN.md tokens and components.
- [ ] No secrets, no real-world brands/people, no fake features.
- [ ] Docs updated; unfinished parts clearly flagged.

---

## 9. Git

* Conventional-commit style messages: `feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:`, `art:`, `content:` — imperative, ≤ 72-char subject, body explains why.
* One logical change per commit; do not mix refactors with features.
* Never commit `.env`, keys, generated build output, large binaries outside `client/public/assets/` and `art/`, or personal data.
* Do not rewrite shared history (no force-push to `main`).
* Commit and push only when the person directing the work asks you to.

---

## 10. Quick reference: common tasks

| Task | Where | Also update |
|---|---|---|
| New game mechanic | `client/game/systems/<name>-system.ts` + rules constants + commands/events | Tests, ARCHITECTURE.md §9–10 if shapes change, save migration if saved |
| New computer app | `client/ui/computer/apps/<app-id>/` + manifest in `registry.ts` | DESIGN.md §9.6 entry (purpose, layout, states); §14 if a new component is needed |
| New character | `shared/content/characters/<id>.ts` + portrait from the kit (DESIGN.md §11: 18 frames) + voice profile | Content validation must pass; DESIGN.md §11.8 checklist |
| New upgrade/research/event | `shared/content/...` using modifiers | Content validation; never special-case in systems |
| New API route | `server/routes/` + schema in `shared/schemas/api/` + client call in `client/services/` | Integration test, ARCHITECTURE.md §6.2 |
| New AI/voice provider | `server/ai/<name>-llm.ts` or `server/voice/<name>-tts.ts` + factory branch | `.env.example`, ARCHITECTURE.md §7/§8/§13/§22 |
| New env var | `server/config.ts` schema | `.env.example`, ARCHITECTURE.md §13, README |
| New visual component | DESIGN.md §14 spec → `client/ui/components/` → gallery | `design/reference/index.html` |
