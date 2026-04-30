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

- [x] Track bigram and trigram weaknesses, not only single letters.
  - When a typo lands on letter X and the previous correct letter was Y, increment a `letterMistakes["yx"]` (and trigram) counter alongside the existing single-letter tracking.
  - Surface the top weak bigrams in lifetime stats and drill suggestions next to the letter list.
  - Most typing pain lives at the bigram level (th, ing, qu, ed); single-letter tracking misses it entirely.

- [x] Fix the Net WPM calculation to count in-progress correct keystrokes.
  - Current formula at `index.html:2620` only counts chars from *completed* targets — partial progress on a half-typed final word never scores.
  - Switch to `((correctTyped - mistypedKeys) / 5) / minutes` so the live HUD value reflects what the player is actually doing.
  - Validate against a 60s clean run vs a 60s run with deliberate typos to confirm the spread matches Monkeytype norms.

- [x] Add "Quit to Menu" to the pause menu.
  - The original todo gated this on meaningful menu state — with PBs, lifetime stats, drill, settings, and run-mode selection, that bar is now cleared.
  - Behavior: end the run without recording it (or record as abandoned) and return to the start screen.

- [x] Make Daily mode actually feel daily.
  - Seed by UTC date instead of local date so a run is shared across timezones (`index.html:2350`).
  - Persist `dailyResult-YYYY-MM-DD` to localStorage and show "Today's score: X — come back tomorrow" on the start screen when set, with a one-tap copy of a shareable string ("TypeQuest Daily 2026-04-30 · 78 NetWPM · 96%").
  - Track and display a daily streak counter; missing a day resets it. Streaks are the entire reason daily modes exist.

- [x] Add a true rarer-word pool for Hard.
  - `commonWords.slice(-260)` is still the tail of english-1k — common-ish. Bring in a curated rarer pack (~500 words: rarer roots, longer compounds, sciencey vocab) and route Hard there.
  - Easy/Normal stay where they are; the difficulty curve currently leans almost entirely on speed/spawn rate, not familiarity.

- [x] Add a sentence / passage typing mode.
  - New run mode "Practice" that streams full sentences (literature, quotes, common phrases) as one long target instead of single words.
  - Disables threats, focuses purely on sustained typing — pairs with the runner mode rather than replacing it.
  - Most typing trainers have this for a reason: real prose has rhythm and capitalization patterns that single-word drills don't reproduce.

- [x] Add a checkpoint or continue option in Adventure mode.
  - Dying on level 4 currently forces a restart from 1, which is the main reason players abandon Adventure.
  - Cheapest version: on game over in Adventure with at least one boss defeated, offer "Continue from Level N" for a score penalty (e.g. -50%).

- [x] Add achievement / milestone toasts.
  - First 60 WPM, first 50-streak, first boss kill, first daily streak of 7, first Hard run cleared, first PB beaten this week, etc.
  - Surface as an unobtrusive corner toast during play and a checklist on the start screen.
  - Cheap to implement, very high engagement leverage; gives early players a reason to come back tomorrow.

- [x] Persist recent run history (last 10 runs).
  - Date, mode, difficulty, net WPM, accuracy, streak. Show as a compact list under the lifetime panel.
  - PBs alone hide the trend ("am I getting faster or just lucky on one run?"); recent runs answer that.

- [x] Add reset controls in Settings.
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

## Visual overhaul — gothic grunge theme

Complete restyle to match the painterly Darkest-Dungeon-adjacent reference at `C:\Users\Admin\alexpach\assets\img\typing_game.png` — deep void blacks, hot ember bloom in the lower-left, tarnished gilt streaks on the right edge with a hint of a Gothic arched window, heavy oil-spatter grit throughout. The current visuals are saturated cyan/navy/green pixel-runner — this section rebuilds the look from the palette outward. Items are foundation-first and dependency-ordered: tackle them in sequence so each layer has solid ground under it before the next lands. Each item lists scope, exact file/line touchpoints, and an acceptance line so a future agent can pick it up cold without re-reading the whole game.

- [ ] Pin the visual direction as a living reference at the top of `index.html`.
  - Add a `<!-- VISUAL DIRECTION -->` comment block immediately above `<style>` (around index.html:13) describing the reference image path, the one-line mood ("painterly gothic grunge — deep void with ember glow and tarnished gilt"), the seven palette token names with intent, the typography stack, and the rule that no new code may introduce ad-hoc hex literals — every color must reference a token.
  - This anchors every later decision; future contributors read it before touching visuals so the look does not drift back toward the saturated runner palette.
  - Acceptance: anyone opening `index.html` cold can answer "what is this game supposed to look like?" without scrolling past the comment.

- [ ] Define the seven-token gothic palette in CSS variables and a JS palette object.
  - In `index.html:14-25`, replace the current 9 vars (`--ink, --muted, --panel, --panel-2, --line, --gold, --leaf, --danger, --sky`) with seven core tokens plus two derived accents: `--void` (#0a0807), `--ash` (#1a1614), `--soot` (#2a221f), `--ember` (#b8341e), `--coal` (#ff7a2e), `--gilt` (#c8923a), `--bone` (#d8c8a0), `--bruise` (#2a1830), `--iron-red` (#4a1814).
  - Mirror the same names as a `palette` object near the top of the script block (somewhere ahead of `levelThemes` at index.html:1727) so canvas code can read `palette.ember` instead of `"#b8341e"`.
  - This is the single source of truth — every later item depends on it.
  - Acceptance: `:root` exposes only the new tokens, and the `palette` JS object exports the same set with identical values.

- [ ] Migrate every hardcoded color literal in the file to palette tokens.
  - Sweep the entire `<style>` block: every `#`-prefixed hex literal and `rgb(`/`rgba(` call gets remapped to the closest palette token (or to `color-mix(in srgb, var(--token) 70%, transparent)` for translucent variants). Touchpoints include `canvas { background: #101426 }` (index.html:98), `.menu { background: #141b30 }` (index.html:119), the button face `background: #25304f` (index.html:170), every `#ffe28b` heading-gold (index.html:127, 130, 262, etc), and every `#ddd5b5` muted body color.
  - Sweep the entire `<script>` block: `drawBackground`, `drawAtmosphere`, `drawHero`, `drawEntity`, `drawWordBubble`, `drawComboBurst`, `drawCountdown`, plus the `spritePalettes` block at index.html:1431-1442. Anything that legitimately needs a shade outside the seven tokens gets added as a derived palette key — never inlined.
  - This must land before any chrome/sprite reskin so the later items can simply rebind tokens instead of hunting hex codes.
  - Acceptance: a regex search for `#[0-9a-fA-F]{3,6}` across `<style>` and `<script>` finds zero hits except the favicon SVG data URI on line 12.

- [ ] Reframe the five `levelThemes` blocks as shared-mood gothic variations.
  - At `index.html:1727-1805`, the five themes (forest, ruins, lava, sky, castle) currently swing between saturated greens, blues, and oranges. Rewrite each so they share the gothic base (`void`/`ash`/`soot` for sky and ground) and differentiate via accent + atmosphere only: forest → mossy bone-green silhouettes with dust motes, ruins → falling ash on tarnished gilt, lava → ember-pit with rising cinders, sky → bruised twilight with thin fog, castle → cathedral interior with candle-flicker.
  - Keep the existing keys (`sky`, `sun`, `far`, `farTop`, `mid`, `midTop`, `ground`, `groundTrim`, `shadow`, `atmosphere`, `hazards`) so no downstream draw code breaks. Every value must reference a palette token, not a literal.
  - Acceptance: progressing through all five Adventure levels feels like one continuous descent into the same world, not five different games.

- [ ] Replace the flat sky gradient with a layered painterly backdrop.
  - `drawBackground` at index.html:3996-4051 currently lays down a 4-stop linear gradient + a 36x36 sun rectangle + flat parallax silhouettes. Rebuild as: (a) a `--void` base fill, (b) a radial ember bloom anchored at the lower-left (centered around x=120, y=460 on the 960x540 canvas) drawn with `globalCompositeOperation = "lighter"`, (c) a tarnished-gilt vertical streak overlay on the right edge masked by procedural noise, (d) a corner vignette darkening the four edges by ~30%.
  - To avoid per-frame cost, bake the static parts (vignette + ember bloom + gilt streak) into an offscreen canvas once on theme change and `drawImage` each frame. Only the parallax/atmosphere remain dynamic.
  - Drop the 36x36 sun rectangle entirely (index.html:4006-4007) — the bright sun does not fit the mood. Replace with a faint moon disc only on `castle` and `sky` stages.
  - Acceptance: the canvas backdrop alone (sprites hidden) reads as the reference image at first glance.

- [ ] Repaint parallax silhouettes as Gothic architecture, not abstract blocks.
  - The `for (let i = 0; i < 8; i++)` mid-far loop at index.html:4014-4020 paints rectangular blobs via `pixelRect`. Replace with hand-authored silhouette polygons drawn via `ctx.fill()`: tall pointed-arch Gothic windows for `castle` and `ruins`, ruined spires with broken tops for `forest` and `sky`, jagged volcanic columns for `lava`. Use 2-3 polygon shapes per theme, colored from `theme.far` / `theme.mid`.
  - Keep the `parallaxX` math intact — only swap the geometry under it.
  - The closer parallax loop at index.html:4021-4038 (forest trees / sky rocks / castle torches) gets the same treatment: forest tree blobs become thin dead-tree silhouettes, sky rocks become crumbling stone pillars, the `else` branch (castle/ruins/lava) becomes broken pew silhouettes and shattered statue fragments. Keep the torch helper (index.html:3903-3910) but recolor flames to ember tones — no cyan highlights.
  - Acceptance: the parallax layers read as architecture, not as solid-color rectangles.

- [ ] Re-skin atmospheric effects to mood-appropriate variants.
  - `drawFireflies` (index.html:3972-3982): rename to `drawDustMotes` and rework as bone-white particles drifting downward slowly, no glow halo. Used by forest now.
  - `drawEmbers` (index.html:3961-3970): keep but tone the alpha curve (current 0.24-0.70 is too bright); cap at ~0.5 and add a slight upward drift jitter so cinders look like they are rising from the ember pit, not floating sideways.
  - `drawRain` (index.html:3948-3959): keep direction and density but switch stroke color from `rgba(166, 189, 204, 0.42)` to `rgba(216, 200, 160, 0.18)` — rain reads as falling ash, not water. Used by ruins.
  - `drawDayNightOverlay` (index.html:3925-3946): drop the moon + star sparkle. Replace with a slow-pulsing dark vignette eased between 0.05 and 0.18 opacity over a ~14s cycle so castle/sky stages get a candle-flicker feel.
  - In `drawAtmosphere` (index.html:3984-3994), update the `type → fn` dispatch table to match the renamed functions, and update each theme's `atmosphere.type` string in `levelThemes`.
  - Acceptance: each theme's particles feel native to the mood — no leftover cute fireflies on a gothic backdrop.

- [ ] Re-texture the ground plane.
  - The ground draw at index.html:4039-4046 is a flat 96px strip (`pixelRect(0, 444, canvas.width, 96, theme.ground)`) plus a trim band and a small shadow strip. Replace with: (a) a base `--soot` strip, (b) a tiled grunge overlay drawn from a 256x96 noise/spatter texture baked once into an offscreen canvas (procedural: stippled pixels at low alpha) tiled across the width using the existing horizontal scroll math, (c) an `--ember` accent band on `lava` only, replacing the current `#ff9a3c` strip at index.html:4044.
  - Keep the same vertical extents (444-540) so collision math stays unaffected.
  - Acceptance: the ground reads as cracked stone or packed ash, not as a flat painted strip.

- [ ] Repaint every sprite palette to gothic tones.
  - At index.html:1431-1442, rework each palette while keeping the same letter codes so the `sprites` block at index.html:1444-1538 needs no changes:
    - `hero` / `heroFlash`: armor `B` from steel-blue `#2e83c5` to dim iron `#3a3a48`, accents `D` to deeper iron, hair/skin `S` warmer (`#c89464`), eyes `G` from `#ffd271` to `--gilt`, cloak `R` from `#9b4b62` to `--ember` so the hero reads as a haunted templar.
    - `grunt`: shift the magenta-pink `M`/`L` (`#86465d` / `#ba5c6d`) to ash-violet (`#5a4660` / `#7a5070`) so enemies look corrupted, not cartoony.
    - `bat`: keep dark but desaturate — `#70417b` → `#3a2a4a`, `#b9658f` → `#503848`.
    - `shielded`: shield `A` / `B` from `#9fb0c9` / `#52627e` to `--bone` over tarnished `--soot`.
    - `obstacle`: rocks from grey `#6b6f7e` to `--ash` / `--soot` with a subtle `--iron-red` speck.
    - `bridge`: timber from `#7b5a38` / `#c19456` to charred dark wood.
    - `chest`: keep gold but use `--gilt` instead of `#d09a4c` and `--bone` instead of `#ffe28b` so even treasure looks aged.
    - `boss`: deep `--bruise` body, `--ember` eyes, `--gilt` crown — boss reads as a fallen king, not a purple ogre.
  - `heroFlash` is the hit-flash palette; brighten each mapped color toward `--bone` rather than pure white so the flash matches the painterly tone.
  - Acceptance: every actor reads in the new mood at a glance, no leftover cyan/magenta on any sprite.

- [ ] Tone the slash and impact effects.
  - `drawHero` attack arc at index.html:4063-4068 paints a cyan blade highlight `#d8edf2` plus a gold spark `#f5c452` plus translucent yellow `rgba(255, 226, 139, *)`. Rebind to: blade `--bone`, sparks `--gilt`, translucent overlay `--ember`. Keep the geometry.
  - On enemy defeat, push 6-10 small `--iron-red` rects into `state.effects` with random vx/vy, gravity 240, duration 0.3 — a brief blood-mist using the existing particle system at index.html:3857-3865.
  - `drawEffects` `slash` branch at index.html:4077-4087: outer arc stroke from `effect.color` to `--ember`, inner highlight from `#ffe28b` to `--bone`. Bump `lineWidth` slightly so the arc reads heavier in the new palette.
  - Acceptance: clearing an enemy reads as a brutal cleave, not a sparkle burst.

- [ ] Tone the combo-burst rings.
  - `drawComboBurst` at index.html:4333-4356 currently strokes a `#ffe28b` inner ring and a `#67c7df` outer cyan ring, with the label drawn in `#ffe28b` over a `#512d47` magenta drop shadow. Rebind to: inner ring `--gilt` drawn additively (`globalCompositeOperation = "lighter"`), outer ring `--ember`, label drop-shadow `--bruise`, label fill `--bone`. Drop the radius slightly (currently 32-78) to ~24-58 so the burst sits below the menu typography weight.
  - Acceptance: combo bursts feel celebratory but stay within the gothic palette — no leftover yellow-on-cyan.

- [ ] Rebind powerup color coding to gothic-coded equivalents.
  - At index.html:4129-4134 the powerup color map is `time:#67c7df, focus:#9ee37d, shield:#b9c8ff, magnet:#f5c452`. Rebind to: `time → --bone` (pale ash), `focus → --bruise` lightened (cold violet), `shield → --gilt` (pale brass), `magnet → --ember` (hot crimson). All four must remain visually distinct at 32px — verify by spawning all four side-by-side in a smoke test before committing.
  - If any onboarding wording references the old colors (start-screen "Powerups" card at index.html:1057-1060, in-game HUD at index.html:935), update it.
  - Acceptance: a player can identify each powerup by color at a glance in the new palette without re-reading the legend.

- [ ] Typography pass — Gothic webfonts with `font-display: swap`.
  - **Lands BEFORE the HUD chrome and overlay reskin** so those items size padding and line-heights against the final font metrics — otherwise both will need a second pass after the fonts swap.
  - Add a `<link>` to Google Fonts (or self-host) for `Cinzel` (700/900 for h1/h2 and HUD labels), `IM Fell English` (400/400i for body prose and bubble text), and `Inconsolata` (700 for HUD numerics — a gothic display face is unreadable at 22px on counters).
  - Replace `font-family: "Trebuchet MS", Verdana, sans-serif` at index.html:39 with `font-family: "IM Fell English", Georgia, serif`. Add explicit families on `h1, h2` (Cinzel) and `.stat strong` (Inconsolata).
  - Update every `ctx.font = "..."` string in canvas code (index.html:4166, 4202, 4350, 4363, 4368) to match: bubble text becomes `'bold 22px "IM Fell English", Georgia, serif'`, countdown becomes `'bold 92px "Cinzel", serif'`, boss-health label becomes `'bold 16px "Cinzel", serif'`.
  - Acceptance: load the page with throttled network — the font swap feels graceful, no panel layout jump greater than 4px.

- [ ] Restyle the HUD chrome.
  - The 8-column HUD strip at index.html:927-936 is styled by `.hud` and `.stat` (index.html:57-85) with a navy gradient `linear-gradient(180deg, #17203a, #10172c)` and a `#3d496d` border. Rebuild as: (a) `background: var(--ash)` plus the same tiled grunge overlay sprite reused from the ground retexture (item 8), (b) a `--gilt` 1px hairline divider between cells, (c) `--bone` for label `<span>`s, `--gilt` for numeric `<strong>`s, (d) a thin `--ember` underline that thickens when a stat changes — toggle a CSS class in the existing `updateHud` flow.
  - Drop the `border-bottom: 1px solid #3d496d` in favor of a `--gilt` filigree (a CSS-only flourish via `::after` or a 4px tall inline-data SVG `background-image`).
  - Acceptance: HUD reads as a tarnished iron plate inset into the game shell, not as a glassy navy bar.

- [ ] Reskin every overlay (start, onboarding, settings, gameOver, pause).
  - `.overlay` (index.html:101-108): change the backdrop `rgba(7, 10, 18, 0.72)` to `rgba(10, 8, 7, 0.84)` so it leans warmer-black and matches the canvas mood.
  - `.menu` (index.html:114-122): replace `border: 1px solid #526087` and `background: #141b30` with a tattered parchment-on-iron motif — `background: var(--ash)`, double-border (outer `--soot`, inner `--gilt` 1px), with a faint parchment SVG inline as a `background-image` overlay clipped to the dialog.
  - `h1, h2` (index.html:124-132): drop the magenta `text-shadow: 4px 4px 0 #512d47`. Replace with `text-shadow: 0 2px 0 var(--void), 0 0 12px rgba(184, 52, 30, 0.4)` so headings glow with a faint ember undertone.
  - Buttons (index.html:166-188): face from `#25304f` to `var(--soot)`, border `#65739d` to `var(--gilt)`, hover outline from `#f5c452` to `var(--ember)`, active state from magenta `#9b4b62` to `var(--ember)` background with `var(--bone)` text. The `.primary` button (index.html:213-219) goes from `#2f8559` green to `var(--gilt)` on `var(--bruise)`.
  - Onboarding cards (index.html:403-421), best-panel (423-451), lifetime-panel (446-457), recent-runs panel (459-490), drill-panel (320-380), achievement-panel, daily-panel: same recipe — `--soot` background, `--gilt` accent border, `--bone` body text, `--gilt` headings.
  - The `.typebar` (index.html:940), `.achievement-toast-stack` (941), `.new-best-banner` (1115), and pause/gameOver dialogs follow the same pattern.
  - Acceptance: navigating through every overlay during a single run feels like one continuous painted UI — no leftover navy/teal panels survive.

- [ ] Repaint in-game word bubbles.
  - `drawWordBubble` at index.html:4247-4276 currently fills bubbles with `#37476c` (target) / `#253049` (non-target) over a `#111827` border, with a `#ffe28b` gold border on the active target and a downward triangle pointer in `#ffe28b`. Move to: bubble fill `var(--void)`, inner highlight `var(--ash)`, active target gets a 2px `var(--gilt)` border, the pointer becomes an `--ember` chevron. Pending text from `#ffe9a6` to `var(--bone)`. Typed-correct text from `#7cf4a1` (cyan-green) to `var(--gilt)` so completed strokes read as struck gold.
  - The expected-letter flash color cycle at index.html:4268 (`#ffdf82` ↔ `#ff6a61`) becomes `--bone` ↔ `--ember` — same alternation, gothic palette.
  - Acceptance: word bubbles feel inscribed on iron plaques, not painted on cyan ribbons.

- [ ] Use the reference image as the title-screen artwork.
  - Copy `C:\Users\Admin\alexpach\assets\img\typing_game.png` into the project at `C:\Users\Admin\typing-game\assets\img\title.png`. Confirm `.gitignore` does not exclude the new path.
  - Target only `#startScreen` (index.html:943) in CSS: set `background-image: url("assets/img/title.png")` with `background-size: cover` and `background-position: center`, layered over the existing dark `rgba(10, 8, 7, 0.72)` overlay so the dialog still has separation. Optionally apply the same backdrop to `#gameOverScreen` so run-end matches.
  - Swap `<meta name="theme-color">` from `#0b1020` (index.html:11) to `#0a0807` so the browser chrome bar matches.
  - Acceptance: the very first screen the player sees is unmistakably the reference image, no compromise.

- [ ] End-to-end visual QA pass.
  - Walk through every game state once: menu → countdown → run with one of each entity type spawned (enemy, flying, shielded, bridge, obstacle, chest, powerup, boss) → pause → settings → gameOver → restart. Capture a screenshot of each.
  - Sweep the screenshots for any leftover stock blue/green/teal that escaped the migration — every visible pixel should fall inside the seven-token palette plus the four powerup accent colors.
  - Add a `Visual QA` checklist near the top of `todo.md` checking off each state with a date stamp; future visual refactors can re-run the same checklist.
  - Acceptance: screenshots from every state read as a single coherent visual identity — Darkest-Dungeon-adjacent gothic grunge, no cyan/teal residue.
