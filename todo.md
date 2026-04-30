# TypeQuest Runner Improvement Roadmap

To do, ordered by impact (highest first):

- [x] Add persistent personal bests per difficulty.
  - Best Net WPM, best Raw WPM, best accuracy, longest streak, top score — keyed by difficulty in `localStorage`.
  - Show on the start screen; flash a "New Best!" banner on the game-over screen when beaten.
  - This is what turns a one-off game into a trainer. Highest-impact remaining item.

- [ ] Add a pause menu.
  - `Esc` pauses. Show Resume and Restart only — no "Exit to Menu" until there is a meaningful menu state to return to.
  - Practice sessions get interrupted by every notification and tab switch; without pause the run is just lost.

- [ ] Add a mute toggle.
  - Single binary toggle, persisted to `localStorage`. Volume slider is overkill for ~5 sounds and unused in serious typing apps.
  - Reachable from the start screen and the pause menu.

- [ ] Add lifetime typing stats.
  - Cumulative words typed, total runs, total time on task.
  - Persistent letter-mistake heatmap that aggregates across runs (extends the existing per-run weakness tracker).
  - Gives players a real "am I improving?" signal, which the per-run breakdown alone cannot provide.

- [ ] Tier word pools by frequency, not only by length.
  - Easy: top ~200 most-common words for muscle-memory drilling.
  - Normal: full english_1k (current behavior).
  - Hard: a rarer pack (e.g. english_5k tail) so the difficulty curve is about word familiarity, not just letter count.

- [ ] Add a one-screen onboarding overlay.
  - Triggered on first load (gated by a `localStorage` flag) and reachable from a "How to play" link on the start screen.
  - Cover: target priority (always the next threat), hearts, streaks, powerups, what each entity type means.
  - Broad first-impression impact — every new player needs this once.

- [ ] Add a keypress sound theme picker.
  - Options: mechanical click, soft tap, none. Persist choice.
  - Signature feature of typing trainers (Monkeytype, keybr) — players hear this thousands of times per session, so letting them tune it is unusually high-impact for the work involved.

- [ ] Add a settings panel.
  - Mute toggle, sound theme, default typing mode (beginner/advanced/expert), default difficulty.
  - Reachable from start screen and pause menu.
  - Becomes valuable once the persistence and audio items above are in place.

- [ ] Add a targeted drill mode.
  - Player picks (or auto-suggests from their lifetime mistake heatmap) a letter or bigram, and the next run filters words containing it.
  - Single highest-value mode for players who actually want to fix specific weaknesses.

- [ ] Add inline difficulty descriptions on the start screen.
  - One-liner under each option so players choose deliberately. No separate modal.
  - Easy: short common words, slow threats, forgiving timers.
  - Normal: balanced runner pacing.
  - Hard: longer/rarer words, dense threats, strict pacing.

- [ ] Refocus sound effects on typing-loop events.
  - Keep: keypress tick, word complete, typo error.
  - Drop the sword / jump / damage / level-up / game-over flavor sounds — those serve the action skin, not the typing practice.

- [ ] Add a favicon and page metadata.
  - Small pixel key/sword icon, `<title>`, and a basic `<meta name="description">` / `og:title` for shareable links.

- [ ] Add a "best on desktop" notice for touch devices.
  - Detect via `(pointer: coarse)` and show a small banner — typing-practice on a phone keyboard is a frustrating first impression.
