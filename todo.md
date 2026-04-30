# TypeQuest Runner Improvement Roadmap

## Priority 0: Fixes And Tuning

- [x] Rebalance the 2x difficulty increase after playtesting.
  - Current values make Hard extremely aggressive because base speed, spawn rate, and level scaling all compound.
  - Target: Easy should be playable for beginners, Normal should pressure average typists, Hard should demand sustained focus without feeling random.

- [x] Add a short countdown before movement starts.
  - Gives players time to put their hands on the keyboard after pressing Start.
  - Prevents the first word from feeling unfair.

- [x] Prevent impossible spawn clusters.
  - Add minimum spacing between active entities based on current speed, word length, and difficulty.
  - Avoid situations where two words arrive so close together that completing the first still guarantees failure on the second.

- [x] Make the first 10 seconds easier on every difficulty.
  - Ramp into full speed and spawn rate over time.
  - This improves onboarding and makes early deaths feel less abrupt.

- [x] Fix the level-up timing order.
  - `speed` is currently computed before `level` updates in the frame.
  - Reorder the update so level changes affect the same frame consistently.

- [x] Remove unused state fields.
  - `heroY` is currently unused.
  - Keep state small so future mechanics are easier to reason about.

## Priority 1: Core Gameplay

- [x] Add health instead of instant death.
  - Give the player 3 hearts.
  - Missing a word costs a heart, knocks the hero back, and clears the current typed input.
  - Instant death can remain an optional Hardcore mode.

- [x] Add word categories by action type.
  - Obstacles: movement words like `leap`, `vault`, `dash`, `climb`.
  - Enemies: combat words like `slash`, `strike`, `guard`, `blast`.
  - Treasure/powerups: reward words like `gold`, `bonus`, `haste`.

- [x] Add more entity types.
  - Flying enemies that require typing before they dive.
  - Shielded enemies that require two short words.
  - Crumbling bridges that use longer timing windows.
  - Treasure chests that are optional but boost score.

- [x] Add powerups.
  - Time boost: adds 5-10 seconds.
  - Focus: slows the world briefly.
  - Combo shield: preserves streak after one typo.
  - Coin magnet: bonus score for the next few words.

- [x] Add combo milestones.
  - Every 5 clean words: small score multiplier.
  - Every 10 clean words: time bonus and visual burst.
  - Every 25 clean words: temporary speed boost or score frenzy.

- [x] Add boss encounters.
  - Every 3 levels, pause normal spawning and show a larger enemy.
  - Bosses require a sequence of words or short phrases.
  - Show a boss health bar and attack telegraphs.

- [x] Add level themes.
  - Forest, ruins, lava cave, sky bridge, castle gate.
  - Each theme can introduce one new hazard pattern.

- [x] Add win condition options.
  - Endless mode: survive as long as possible.
  - Adventure mode: clear 5 themed stages and defeat a final boss.
  - Daily challenge: fixed seed and word list for comparable scores.

## Priority 2: Typing Experience

- [x] Improve word progression feedback.
  - Highlight typed letters directly in the word bubble above the current target.
  - Keep the bottom typebar, but make the in-world target readable without looking away from the action.

- [x] Add typo recovery rules.
  - Option A: wrong key damages streak but does not reset typed progress.
  - Option B: wrong key flashes the expected letter and pauses input briefly.
  - Pick the version that feels more like a typing trainer than a punishment trap.

- [x] Add support for punctuation and capitalization modes.
  - Beginner: lowercase words only.
  - Advanced: mixed case, punctuation, short phrases.
  - Expert: symbols and numbers.

- [x] Add adaptive difficulty.
  - Increase word length and spawn pressure when accuracy is high.
  - Ease pressure if accuracy drops below a threshold.
  - Keep manual difficulty levels, but let them tune the adaptive range.

- [x] Track raw WPM and net WPM.
  - Raw WPM: all typed characters.
  - Net WPM: correct completed words minus typo penalty.
  - Final stats should show both for typing-practice value.

- [x] Add per-run typing breakdown.
  - Total keystrokes.
  - Correct keystrokes.
  - Mistyped letters.
  - Longest streak.
  - Average reaction time per word.

- [x] Add letter-specific weakness tracking.
  - Track which expected letters are most often mistyped.
  - Show a small post-game summary like `Needs work: r, t, p`.

## Priority 3: Visual And Game Feel

- [x] Replace simple rectangle sprites with richer pixel art sprites.
  - Hero idle/run/jump/attack frames.
  - Enemy walk/hit/death frames.
  - Obstacle variants such as spikes, rocks, pits, logs, and traps.

- [x] Add sprite animation frames.
  - Use small arrays of pixel rects or embedded sprite sheets.
  - Keep assets self-contained in the HTML if the single-file requirement remains.

- [x] Add hit effects.
  - Sword slash arc.
  - Enemy impact flash.
  - Dust puff when jumping.
  - Screen shake on damage.
  - Pixel particles on successful words.

- [x] Add parallax depth.
  - Current background has some movement, but layers can be more distinct.
  - Add clouds, far mountains, mid-ground ruins, foreground grass, and torch lights.

- [x] Improve word bubble layout.
  - Prevent overlapping bubbles when entities are close.
  - Move secondary target bubbles higher or fade them slightly.
  - Add an arrow or outline to make the current target unmistakable.

- [x] Add camera effects.
  - Slight push forward at high streaks.
  - Brief slowdown when a boss appears.
  - Subtle impact zoom on attacks.

- [x] Add day-night or weather variation.
  - Rain in ruins, embers in lava cave, fireflies in forest.
  - Keep effects lightweight so performance stays stable.

## Priority 4: Progression And Persistence

- [ ] Add persistent personal bests per difficulty.
  - Best Net WPM, best Raw WPM, best accuracy, longest streak, top score — keyed by difficulty in `localStorage`.
  - Show on the start screen; flash a "New Best!" banner on the game-over screen when beaten.
  - This is what turns a one-off game into a trainer. Highest-impact remaining item.

- [ ] Add lifetime typing stats.
  - Cumulative words typed, total runs, total time on task.
  - Persistent letter-mistake heatmap that aggregates across runs (extends the existing per-run weakness tracker).
  - Gives players a real "am I improving?" signal, which the per-run breakdown alone cannot provide.

## Priority 5: UI And Menus

- [ ] Add a pause menu.
  - `Esc` pauses. Show Resume and Restart only — no "Exit to Menu" until there is a meaningful menu state to return to.
  - Practice sessions get interrupted by every notification and tab switch; without pause the run is just lost.

- [ ] Add a one-screen onboarding overlay.
  - Triggered on first load (gated by a `localStorage` flag) and reachable from a "How to play" link on the start screen.
  - Cover: target priority (always the next threat), hearts, streaks, powerups, what each entity type means.
  - Broad first-impression impact — every new player needs this once.

- [ ] Add a settings panel.
  - Mute toggle, sound theme, default typing mode (beginner/advanced/expert), default difficulty.
  - Reachable from start screen and pause menu.
  - Becomes valuable once the persistence and audio items below are in place.

- [ ] Add inline difficulty descriptions on the start screen.
  - One-liner under each option so players choose deliberately. No separate modal.
  - Easy: short common words, slow threats, forgiving timers.
  - Normal: balanced runner pacing.
  - Hard: longer/rarer words, dense threats, strict pacing.

## Priority 6: Audio

- [ ] Add a mute toggle.
  - Single binary toggle, persisted to `localStorage`. Volume slider is overkill for ~5 sounds and unused in serious typing apps.
  - Reachable from the start screen and the pause menu.

- [ ] Add a keypress sound theme picker.
  - Options: mechanical click, soft tap, none. Persist choice.
  - Signature feature of typing trainers (Monkeytype, keybr) — players hear this thousands of times per session, so letting them tune it is unusually high-impact for the work involved.

- [ ] Refocus sound effects on typing-loop events.
  - Keep: keypress tick, word complete, typo error.
  - Drop the sword / jump / damage / level-up / game-over flavor sounds — those serve the action skin, not the typing practice.

## Priority 7: Content

- [ ] Tier word pools by frequency, not only by length.
  - Easy: top ~200 most-common words for muscle-memory drilling.
  - Normal: full english_1k (current behavior).
  - Hard: a rarer pack (e.g. english_5k tail) so the difficulty curve is about word familiarity, not just letter count.

- [ ] Add a targeted drill mode.
  - Player picks (or auto-suggests from their lifetime mistake heatmap) a letter or bigram, and the next run filters words containing it.
  - Single highest-value mode for players who actually want to fix specific weaknesses.

## Priority 8: Deployment And Sharing

- [ ] Add a favicon and page metadata.
  - Small pixel key/sword icon, `<title>`, and a basic `<meta name="description">` / `og:title` for shareable links.

- [ ] Add a "best on desktop" notice for touch devices.
  - Detect via `(pointer: coarse)` and show a small banner — typing-practice on a phone keyboard is a frustrating first impression.

## Removed (not high-impact for a typing-practice game)

- Volume slider — single mute toggle covers the real use case.
- Audio ducking during errors and game over — gamefeel polish.
- Run summary chart over time — the per-run breakdown already shows the same numbers; charting is heavy for marginal gain.
- Seed display and copyable challenge code — Daily mode already provides a shared seed once per day, which is the actual use case.
- Splitting the script into internal modules — no user-facing impact while a single author maintains a 2000-line file.
- Replacing magic numbers with named constants — internal cleanup; the existing config blocks already cover most tuning.
- Making difficulty config more expressive — current `palettes` config is already adequate.
- Optimizing per-frame entity sorting — premature; the entity list stays small.
- Phrase mode — sentences don't fit the one-bubble-per-threat mechanic; would be a separate game.
