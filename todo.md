# TypeQuest Runner Improvement Roadmap

To do, ordered by impact (highest first):

- [x] Auto-pause when the window loses focus or visibility.
  - Listen for `blur` and `visibilitychange` and call `pauseGame()` when the run is active.
  - On resume, replay the 3s countdown so the player isn't punished by an off-screen heart loss.
  - Standard expectation in any timed game; without it, alt-tabbing silently kills runs.

- [x] Add a live WPM and accuracy graph to the game-over screen.
  - Sample net WPM and rolling accuracy each second during a run; render a small line chart in the final-grid panel.
  - Highlight the best WPM peak and lowest accuracy dip so players can see *where* they fell apart, not just the average.
  - This is the single most-cited reason serious typists stick with Monkeytype/keybr — summary numbers alone don't teach.

- [ ] Track bigram and trigram weaknesses, not only single letters.
  - When a typo lands on letter X and the previous correct letter was Y, increment a `letterMistakes["yx"]` (and trigram) counter alongside the existing single-letter tracking.
  - Surface the top weak bigrams in lifetime stats and drill suggestions next to the letter list.
  - Most typing pain lives at the bigram level (th, ing, qu, ed); single-letter tracking misses it entirely.

- [ ] Fix the Net WPM calculation to count in-progress correct keystrokes.
  - Current formula at `index.html:2620` only counts chars from *completed* targets — partial progress on a half-typed final word never scores.
  - Switch to `((correctTyped - mistypedKeys) / 5) / minutes` so the live HUD value reflects what the player is actually doing.
  - Validate against a 60s clean run vs a 60s run with deliberate typos to confirm the spread matches Monkeytype norms.

- [ ] Add "Quit to Menu" to the pause menu.
  - The original todo gated this on meaningful menu state — with PBs, lifetime stats, drill, settings, and run-mode selection, that bar is now cleared.
  - Behavior: end the run without recording it (or record as abandoned) and return to the start screen.

- [ ] Make Daily mode actually feel daily.
  - Seed by UTC date instead of local date so a run is shared across timezones (`index.html:2350`).
  - Persist `dailyResult-YYYY-MM-DD` to localStorage and show "Today's score: X — come back tomorrow" on the start screen when set, with a one-tap copy of a shareable string ("TypeQuest Daily 2026-04-30 · 78 NetWPM · 96%").
  - Track and display a daily streak counter; missing a day resets it. Streaks are the entire reason daily modes exist.

- [ ] Add a true rarer-word pool for Hard.
  - `commonWords.slice(-260)` is still the tail of english-1k — common-ish. Bring in a curated rarer pack (~500 words: rarer roots, longer compounds, sciencey vocab) and route Hard there.
  - Easy/Normal stay where they are; the difficulty curve currently leans almost entirely on speed/spawn rate, not familiarity.

- [ ] Add a sentence / passage typing mode.
  - New run mode "Practice" that streams full sentences (literature, quotes, common phrases) as one long target instead of single words.
  - Disables threats, focuses purely on sustained typing — pairs with the runner mode rather than replacing it.
  - Most typing trainers have this for a reason: real prose has rhythm and capitalization patterns that single-word drills don't reproduce.

- [ ] Add a checkpoint or continue option in Adventure mode.
  - Dying on level 4 currently forces a restart from 1, which is the main reason players abandon Adventure.
  - Cheapest version: on game over in Adventure with at least one boss defeated, offer "Continue from Level N" for a score penalty (e.g. -50%).

- [ ] Add achievement / milestone toasts.
  - First 60 WPM, first 50-streak, first boss kill, first daily streak of 7, first Hard run cleared, first PB beaten this week, etc.
  - Surface as an unobtrusive corner toast during play and a checklist on the start screen.
  - Cheap to implement, very high engagement leverage; gives early players a reason to come back tomorrow.

- [ ] Persist recent run history (last 10 runs).
  - Date, mode, difficulty, net WPM, accuracy, streak. Show as a compact list under the lifetime panel.
  - PBs alone hide the trend ("am I getting faster or just lucky on one run?"); recent runs answer that.

- [ ] Add reset controls in Settings.
  - "Reset personal bests" and "Clear lifetime stats" buttons with a confirm step.
  - Currently there's no way out of bad/test data; users on shared machines have no privacy option either.

- [ ] Add configurable run length.
  - Fixed timers per difficulty (105/95/85s) are the only options; expose 30s sprint, 60s standard, 120s endurance as a setting separate from difficulty.
  - Lets players pick a session size that matches their attention budget; 30s is the de-facto warmup norm in typing communities.

- [ ] Smooth out theme transitions between levels.
  - Currently every level swap snaps to a new palette mid-frame.
  - Crossfade sky/ground colors over ~0.6s on level change; theme parameters are already centrally defined so a per-frame interpolation is straightforward.

- [ ] Show a small entity/powerup legend on the start screen.
  - One-row icon strip under "How to play" naming each entity (rock = jump, enemy = attack, chest = optional treasure, etc.) and each powerup color.
  - The onboarding cards explain the categories but don't pair each label with the actual sprite the player will see; players currently learn by losing hearts.
