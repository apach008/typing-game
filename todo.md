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

- [ ] Improve word bubble layout.
  - Prevent overlapping bubbles when entities are close.
  - Move secondary target bubbles higher or fade them slightly.
  - Add an arrow or outline to make the current target unmistakable.

- [ ] Add camera effects.
  - Slight push forward at high streaks.
  - Brief slowdown when a boss appears.
  - Subtle impact zoom on attacks.

- [ ] Add day-night or weather variation.
  - Rain in ruins, embers in lava cave, fireflies in forest.
  - Keep effects lightweight so performance stays stable.

## Priority 4: Audio

- [ ] Add a mute toggle and volume slider.
  - Store preference in `localStorage`.
  - Web Audio effects are useful, but users need control.

- [ ] Add separate sound effects.
  - Keypress tick.
  - Word complete.
  - Sword attack.
  - Jump.
  - Damage.
  - Level up.
  - Game over.

- [ ] Add audio ducking during errors and game over.
  - Makes important events more obvious.

## Priority 5: UI And Menus

- [ ] Add a pause menu.
  - `Esc` pauses.
  - Include Resume, Restart, Mute, and Exit to Menu.

- [ ] Add an instructions panel.
  - Keep it compact.
  - Explain typing target priority, streak bonuses, hearts, and powerups.

- [ ] Add difficulty descriptions.
  - Easy: shorter words, slower threats, forgiving timers.
  - Normal: balanced adventure.
  - Hard: longer words, denser threats, strict pacing.

- [ ] Add high scores.
  - Store best score, best WPM, best accuracy, and longest streak per difficulty in `localStorage`.
  - Show personal bests on the start and game-over screens.

- [ ] Add a run summary chart.
  - Show score and WPM over time with a small canvas or SVG-free custom drawing.

- [ ] Add seed display and copyable challenge code.
  - Lets players replay or share the same run.

## Priority 6: Code Architecture

- [ ] Split the single script into clear internal modules while staying in one HTML file.
  - `config`
  - `state`
  - `input`
  - `audio`
  - `spawning`
  - `scoring`
  - `rendering`
  - `ui`

- [ ] Replace magic numbers with named constants.
  - Hero hit line.
  - Ground height.
  - Entity dimensions.
  - Spawn margins.
  - Action durations.

- [ ] Make difficulty config more expressive.
  - Speed range.
  - Spawn range.
  - Word length range.
  - Timer.
  - Entity mix.
  - Mistake penalty.
  - Heart count.

- [ ] Add seeded randomness.
  - Allows deterministic tests and replayable challenge runs.

- [ ] Avoid sorting entities every frame for current target.
  - Keep entities ordered by spawn position or cache current target.
  - Current list is small, but this becomes cleaner as mechanics grow.

- [ ] Escape dynamic word HTML defensively.
  - The current word list is static and safe.
  - If custom word lists are added, `innerHTML` should not receive raw words.

- [ ] Add a small debug overlay.
  - FPS.
  - Entity count.
  - Current speed.
  - Spawn interval.
  - Current seed.

## Priority 7: Content

- [ ] Expand the word list substantially.
  - At least 100 words per difficulty band.
  - Avoid repeated words in short sessions.

- [ ] Add theme-specific word banks.
  - Forest: `branch`, `moss`, `thorn`, `glade`.
  - Ruins: `relic`, `pillar`, `glyph`, `temple`.
  - Lava: `magma`, `ember`, `ashfall`, `forge`.
  - Castle: `banner`, `turret`, `crown`, `guard`.

- [ ] Add optional educational word packs.
  - Common English words.
  - Programming terms.
  - SAT vocabulary.
  - User-provided custom list.

- [ ] Add phrase mode.
  - Short phrases for advanced players.
  - Use spaces and punctuation as part of the challenge.

## Priority 11: Deployment And Sharing

- [ ] Add favicon.
  - Small pixel sword, boot, or type key icon.
