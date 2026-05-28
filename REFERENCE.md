# Pathologisation — Design & FX Reference

> All changes are made in `twine-twee-edit/story.twee`, then compiled with `python3 twee_to_html.py`.
>
> **Start passage:** always `Title Screen` — enforced by `"start": "Title Screen"` in `StoryData`. Every compile sets `startnode` correctly regardless of what Twine last saved. To confirm in Twine: Title Screen passage should show the rocket/start marker; if not, right-click → Start Story Here.
>
> **Twine → code workflow:** edit in Twine → save → `cd twine-twee-edit && python3 html_to_twee.py` (reads from `pathologisation/index.html`) → make code edits to `story.twee` → `python3 twee_to_html.py`.
>
> **Proof:** `proof.html` is auto-regenerated on every `twee_to_html.py` compile via `twee_to_proof.py`. Run `python3 twee_to_proof.py` standalone to regenerate without a full compile.

---

## 1. PASSAGE TAGS

Add tags in the passage header: `:: Passage Name [tag1 tag2] {...}`

### Visual / Font Tags

| Tag | Effect | Currently used on |
|-----|--------|------------------|
| `titlescreen` | Title screen layout, no home button, no font combos, static background, special link styling | Title Screen |
| `breakdownfont` | Body text at 2.5× size, links pinned to 48px | Park Psychosis |
| `psychosis` | Nimbus Roman body at 1rem (overrides breakdownfont via `!important`), chromatic aberration, cummings body layout (10s reveal delay), wandering smooth1–4 hooks, smooth5 fake escape link, no font combos, no home button, no BD distress effects | Park Psychosis, Public Toilet Psychosis |
| `blue` | Solid blue background (`#0029a3`) via `#bg-layer` — `tw-story` stays transparent so floating images remain visible | GP Reflection |

### Background Image Tags

Add the tag and its image path in `TAG_BACKGROUNDS` (Story JavaScript):

| Tag | Image | Currently used on |
|-----|-------|------------------|
| `waitingroom` | `backgroundwaiting.jpg` | GP Reception |
| `parkinglot` | `parkinglot.jpg` | Car Park |
| `GP` | `gp.jpg` | GP Office 1, GP Reassess |
| `GP2` | `gpoffice.jpg` | GP Confess, GP Lie |
| `parkbench` | `parkbench.jpg` | Park Encounter |
| `static` | `static.gif` | Title Screen |
| `parkpsychosis` | `parkpsychosis.gif` | Park Psychosis |
| `brokentoilet` | `brokentoilet.jpg` | Public Toilet Psychosis |
| `cigarettes` | `cigarettes.jpg` | Cigarette Break |
| `doctorsoffice3` | `gp3.jpg` | GP Office 2 |
| `elevator` | `elevator.jpg` | Elevator |
| `psychward` | `psychward.jpg` | Psych Ward |
| `toilet` | `toilet.jpg` | Public Toilet |
| `ezymart` | `ezymart.jpg` | Ezymart |
| `nightambience` | `nightambience.jpg` | Night Walk |

**To add a new background:** drop the image in `images/`, add one line to `TAG_BACKGROUNDS`, add the tag to your passage.

### Solid Colour Background Tags

Solid colours are routed through `#bg-layer` (not `tw-story`) so floating images (`z-index: -1`) remain visible. `tw-story` gets `has-bg-image` (transparent) automatically.

| Tag | Colour | Notes |
|-----|--------|-------|
| `blue` | `#0029a3` | Defined in `SOLID_COLORS` in `updateBackground()` |

**To add a new solid colour room:** add the tag + hex to `SOLID_COLORS` in `updateBackground()`. The CSS `tw-story[tags~="yourtag"] { background-color: ... }` rule is NOT needed — colour is set by JS on `#bg-layer`.

### Breathing Background Tag

| Tag | Effect |
|-----|--------|
| `breathe` | Image rooms: `#bg-layer` scales/darkens on inhale/exhale (8s cycle). Solid colour rooms: `#bg-layer.colour-breathe` pulses the background-color between shades (e.g. blue → indigo → deep purple) — no filter, so floating images are unaffected. **GIFs never breathe** (too busy). |

Currently used on: Car Park `[parkinglot breathe]`, Park Encounter `[parkbench breathe]`, GP Reflection `[blue breathe]`, Public Toilet `[toilet breathe]`.

**Colour breathe keyframes** (blue room, 8s ease-in-out):
- 0% `#0029a3` → 18% `#2200e0` (electric indigo inhale peak) → 28% `#1a00b8` → 72% `#08003a` → 82% `#04001a` (near-black exhale) → 100% `#0029a3`

### Audio Tags

Auto-plays a looping track when entering, stops when leaving:

| Tag | Track | Currently used on |
|-----|-------|------------------|
| `clockroom` | `clock.mp3` | GP Office 1, GP Confess, GP Lie, GP Reassess |
| `parkbench` | `crickets.mp3` | Park Encounter |
| `nightambience` | `electrichum.mp3` | Night Walk |
| `phonepsychosis` | `electricwhine.mp3` | Phone Psychosis |
| `theftpsychosis` | `siren.mp3` | Theft Psychosis |

**To add a new room track:** add the audio file to `audio/`, register it in `hal.tracks` passage, add one line to `TAG_TRACKS` in Story JavaScript.

### Effect Tags (ready to use, not yet applied)

| Tag | Effect |
|-----|--------|
| `echo` | Ghosted shadow of the passage text drifts slightly offset, slowly animated. JS rewrites it with tense/subject shifts (I→you, was→is, etc.). |
| `dissolve` | ~30% of words in the passage slowly fade to invisible over 3–15s after arrival. |
| `contradict` | Words marked with `data-contradict="alt text"` typewriter-overwrite themselves with the alt, then erase, loop. |

**Usage:**
- Add tag to passage header
- For `contradict`, also wrap words in passage: `<span data-contradict="replacement">original</span>`

### Decor Tag

| Tag | Effect |
|-----|--------|
| `decor` | Marks a passage as a decor definition for another passage. Must be named `"{Passage Name} decor"`. |

**Format of a decor passage:**
```
# comment lines start with #
# Optional first line:
slab: br

Your intrusion text here
all lines become one floating block
| lg

```
Size options: `xxl xl lg md sm xs`

Intrusion text floats in the background in **Nimbus Roman**, large, low opacity, slowly drifting.

**Currently has decor:** GP Office 2 (poem block).  
**To add decor to any passage:** create a new passage named `"Passage Name decor"` with tag `[decor]`.

---

## 2. NAMED HOOKS (Inline, in passage content)

### `|charged>[word]`
Word softly blurs and fades in/out on a 4s cycle. Feels like a word losing focus.

```
The |charged>[resonant] ticks.
```
Currently used in: GP Office 1.

### `|smooth1>` `|smooth2>` `|smooth3>` `|smooth4>`
**Psychosis rooms only.** Text fragments that wander randomly across the screen continuously. Text is injected from a `[fragment-pool]` passage — leave the hook empty in the passage:

```
|smooth1>[ ]
|smooth2>[ ]
```
Font: **Termina**. Currently used in: Park Psychosis, Public Toilet Psychosis.

Wander behaviour: starts moving immediately on arrival (`void el.offsetTop` forces initial position commit before transition). Position range: `top` 5–77%, `left` 3–68% — stays within viewport. Speed: 250–1450ms per move.

### `|smooth5>`
**Psychosis rooms only.** The fake escape link. Hidden for 5s, then appears at a random screen position. After clicking, lingers 2.5s then fades out. Use with `(link-replace:)`.

```
|smooth5>[(link-replace: (either: "leave", "run", "get out"))[(either: "nice try", "you're tripping")]]
```
Font: **DejaVu Sans**. Currently used in: Park Psychosis.

---

## 3. HTML CLASSES (inline in passage content)

### `.dialogue`
Indented italic block for spoken dialogue. Lines inside with `//like this//` are rendered normal weight (attribution/speaker tag).

```html
<div class="dialogue">I don't like how it makes me feel. //you tell the doctor.//</div>
```
Dialogue blocks get a **typewriter effect** on load (character by character), staggered after the scramble animation finishes. Clicking anywhere skips it.

### `floating-img`
Image that drifts slowly around the screen. Starts hidden, pops in within 0–8s at a random position, then wanders slowly. Calm and dreamlike.

```html
<img class="floating-img" src="./images/escitalopram.png" alt="escitalopram">
```

**Architecture:** Images in the passage are only used as a source list. `startFloatingImages()` reads their `src` attributes, **hides the originals** (`el.style.display = 'none'`), then creates fresh `<img>` clones inside a dedicated `#floating-layer` div (position: fixed, inset: 0, z-index: 5 — above `#bg-layer` at z:-1, below `tw-passage` at z:100), and wanders them from there. The `#floating-layer` is cleared on each navigation.

**Why hide originals:** Original `<img class="floating-img">` elements remain in the passage DOM after `startFloatingImages` clones them. They have `position: absolute` from the CSS, so they end up stacked at the top-left of their nearest `position: relative` ancestor (a sentence div in the layout system). The `float-breathe` animation then makes the stack visibly pulse in one spot. Setting `display: none` on each original as its src is collected eliminates this.

**Important:** Wrap the `<img>` tags in a container div — do NOT put them as bare children of the passage root. The layout system (`splitAtSentences`) filters out subgroups with no text content; a bare `<img>` with no surrounding text would be silently dropped from the rebuilt DOM. Wrapping in a `<div>` preserves them as element nodes.
Speed/size configurable in `startFloatingImages()`. Currently used in: GP Reflection.

**Note:** floating images use `position: fixed; z-index: -1`. They only show if `tw-story` is transparent (`has-bg-image` class). Solid-colour and image-backed rooms both add `has-bg-image` via JS, so images always show. Do not give `tw-story` a CSS `background-color` for any room that has floating images.

### `erratic-img`
Image that moves fast and jittery, glitch-flickers opacity and colour. Starts hidden, appears within 0–3s.

```html
<img class="erratic-img" src="./images/static.gif">
```
Currently used in: CRRF passages. CRRF Image 2 uses `nightambience.jpg` as placeholder.

### `.crrf-bg-overlay`
Full-screen background image overlay (z-index below text). Used inside display sub-passages.

```html
<div class="crrf-bg-overlay" style="background-image: url('./images/static.gif')"></div>
```
Opacity: 35%. Currently used in: CRRF Background 1–4.

### `.game-link`
Styled like a `tw-link` (glow, fidget animation, flicker on hover) but on a plain `<a>` tag. Used for external links.

```html
<a class="game-link" href="https://..." target="_blank">Link text</a>
```
Currently used in: Title Screen (Ryu Konrad name, GitHub).

**Title Screen author block structure:**
- `.wk-author-name` — "Ryu Konrad" link, Nimbus Roman, `clamp(1.3rem, 2.5vw, 1.9rem)`
- `.wk-github` — GitHub link, `position: fixed; bottom: 1.2rem; left: 1.4rem`, Nimbus Roman — separate from `.wk-author` so it doesn't affect document flow or cause a scrollbar

---

## 4. AUTOMATIC EFFECTS (always on, no configuration needed)

### Layout randomiser
Every non-psychosis, non-titlescreen passage gets a random indentation mode and a central screen position on each load. Fires on **every navigation** — the same passage looks different every visit.

**Position:** `position: fixed`, `width: 52vw` (max 800px), `top` 5–60vh, `left` 5–23%. Wide vertical range ensures passages don't cluster near the top.

**How fragments are made:**
1. Content is split at `<br>` boundaries (paragraph-level)
2. Each paragraph is further split at sentence boundaries (`. ` `! ` `? `) into sentence-level fragments
3. Each fragment becomes a block `<div>` with `position: relative; left: Xvw`
4. Shifting with `left` (not `padding-left`) keeps every fragment at full width — no narrow columns

**Indentation modes** (one picked at random, `scatter` and `jump` weighted 2×):

| Mode | Pattern |
|------|---------|
| `scatter` | Two-cluster random — lines land near left edge or far right |
| `jump` | Hard alternation: even lines left, odd lines far right |
| `stagger` | Shuffled 6-step pool |
| `cascade` | Steady left-to-right sweep |
| `wave` | Sine-wave curve |
| `reverse` | Right-to-left sweep |

Max indent range: **10–32vw** (re-randomised each load). `tw-link` handlers survive because nodes are moved, not cloned. Dialogue blocks (`.dialogue`) are never split or indented.

Skipped on: `[psychosis]`, `[titlescreen]`.

### Psychosis layout (`[psychosis]` passages only)

Passage anchored: `position: fixed; width: 42vw; top: 8vh; left: 5%`.

Body text reveals in two phases:

**Phase 1 (0–10s):** Body text is hidden (`opacity: 0`). Only smooth1–4 wandering hooks are visible.

**Phase 2 (10s+):** Smooth hooks fade out simultaneously (`opacity 0.8s ease`) while body text fragments reveal one unit at a time at 90ms intervals (typewriter over the scattered layout). The text is pre-processed into cummings-style units:
- **Short words (≤3 letters):** 40% chance of being letter-stacked (each letter its own line). The rest join the word buffer.
- **Long words (≥7 letters):** 28% chance of a mid-word break 2–5 chars in.
- **Normal words:** grouped into chunks of up to 4 per scatter div to control total height.
- **All units** get a bimodal scatter (`left: 0–2.6vw` or `5–12vw`) via `position: relative`.
- Line height: `1.15` (tight, to prevent overflow).

Auto-redirect at 20s fires regardless. Navigating away cancels the reveal cleanly. smooth1–5 hooks are untouched and re-appended intact.

**CSS transition gotcha — why the hook fade uses `requestAnimationFrame`:** The wander cleanup sets `el.style.transition = 'none'`. If you then set `el.style.transition = 'opacity 0.8s ease'` and `el.style.opacity = '0'` in the same synchronous JS block, the browser collapses both transition assignments into one style recalculation frame — the `none` step is never committed — and the animation may not fire. Wrapping the opacity change in a `requestAnimationFrame` ensures the `none` state is painted first; the following frame then correctly transitions from opacity 1 → 0.

**BD distress effects** (`bd-tremor`, `bd-blur`, `bd-glitch`) are suppressed when `[psychosis]` is present even if `[breakdownfont]` is also tagged.

### Scramble animation
Every passage (except psychosis) animates words in on load. Each word starts displaced from its final position with random rotation (±27.5°), scale (0.25×–2.45×), and offset (±450px x / ±250px y). Words fly to their final position using elastic overshoot easing (`cubic-bezier(0.34, 1.56, 0.64, 1)`) — each word overshoots its landing and snaps back. Settle duration is randomised per word (320–1100ms). Words reveal in **DOM order** (top-to-bottom, left-to-right) at 38ms intervals so the reader can follow from the top as the animation completes. Clicking anywhere skips instantly.

### Link fidget
All links tremble slightly in a continuous micro-animation (`fidget` keyframes, `steps(40)`). Visited links lose the animation and get a strikethrough.

### Link flicker on hover
Rapid colour flash: red → cyan → yellow → magenta → white over 0.35s.

### Breakdown distress (on `[breakdownfont]` passages, NOT `[psychosis]`)
One of three effects is randomly chosen each time:
- `bd-tremor` — shakes horizontally at high speed
- `bd-blur` — pulses in and out of focus
- `bd-glitch` — red/cyan chromatic split, fast

Suppressed entirely when passage also has `[psychosis]` tag.

### Chromatic aberration (on `[psychosis]` passages)
Text-shadow snaps erratically between different red/cyan fringe intensities. Uses `steps(1)` for a sharp, digital glitch feel.

### Background `#bg-layer`
Handles all backgrounds — both image and solid colour. Always present, invisible when no background is active. `tw-story` is always kept transparent (`has-bg-image`) whenever a background is active, so `floating-img` elements (z-index: -1) remain visible. Breathing animations live on `#bg-layer` so they never affect text or floating images via filter stacking context.

---

## 5. AUDIO

### Global background loop
`background.mp3` loops continuously from the first passage. Never stops.

### Manual track calls (in passage content)
```
(track: 'trackname', 'play')
(track: 'trackname', 'loop', true)
(track: 'trackname', 'seek', 5)
(track: 'trackname', 'stop')
```

### Registered tracks

| ID | File | Status |
|----|------|--------|
| `background` | `background.mp3` | Global loop, always playing |
| `clock` | `clock.mp3` | Tag-based (`clockroom`) |
| `crickets` | `crickets.mp3` | Tag-based (`parkbench`) |
| `electrichum` | `electrichum.mp3` | Tag-based (`nightambience`) |
| `electricwhine` | `electricwhine.mp3` | Tag-based (`phonepsychosis`) |
| `siren` | `siren.mp3` | Tag-based (`theftpsychosis`) |
| `psychobirds` | `psychosisbirds.mp3` | Manual — `(track: 'psychobirds', 'play')` in Park Psychosis |
| `printer` | `printer.mp3` | Manual — `(track: 'printer', 'seek', 5)` in New Medication |

---

## 6. HARLOWE MACROS IN USE

### Navigation
```
[[Passage Name]]                    — link with passage name as text
[[Text->Passage Name]]              — link with custom text
[[Passage Name<-Text]]              — same (text on the right side)
(goto: "Passage Name")              — instant redirect, no click needed
(live: 20s)[(goto: "Passage Name")] — auto-redirect after 20 seconds
```

### Randomisation
```
(either: "a", "b", "c")            — picks one at random each render
(display: (either: "P1", "P2"))    — displays a random sub-passage
```

### Interaction
```
(link-replace: "text")[replacement] — click replaces text with replacement, no navigation
```

### Audio (HAL)
```
(track: 'id', 'play')
(track: 'id', 'stop')
(track: 'id', 'loop', true)
(track: 'id', 'seek', seconds)
```

### Sub-passage display
```
(display: "Sub-passage Name")       — embeds another passage's content inline
```

---

## 7. FONT SYSTEM

### Typekit kit
`@import url('https://use.typekit.net/vnl6sno.css')` — loads all custom fonts.

### Font combo randomiser
On every passage load (except `[titlescreen]` and `[psychosis]`), a random combo is applied:

| Combo | Body | Links | Dialogue |
|-------|------|-------|---------|
| 1 | Nimbus Roman | DejaVu Sans | Logic Monospace |
| 2 | Termina | Nimbus Roman (1.5em) | DejaVu Sans |
| 3 | Logic Monospace | DejaVu Sans | Nimbus Roman |
| 4 | DejaVu Sans | Termina | Nimbus Roman |

Dialogue always stays italic — only the family changes per combo. Attribution lines (`//like this//` → `em`/`i` inside `.dialogue`) are non-italic and use the **body text font** for that combo, not the dialogue font — set via per-combo `.dialogue em` overrides.

### Font sizes

| Element | Size |
|---------|------|
| Global base (`tw-story`) | `1em` (browser default ≈ 16px) |
| Breakdownfont body | `2.5em` |
| Breakdownfont links | `48px !important` |
| Psychosis body (`tw-passage`) | `1rem !important` — escapes breakdownfont's 2.5em via rem |
| smooth1–4 hooks | `0.9rem !important` — overrides breakdownfont |
| smooth5 + its link | `1.25rem` |
| UI buttons (home, fullscreen) | `0.9rem` |
| Title screen links | `0.9rem` |
| Title screen author name | `clamp(1rem, 2vw, 1.4rem)` |
| Title screen description | `clamp(0.7rem, 1.4vw, 0.9rem)` |
| Combo 2 Nimbus Roman links | `1.5em` |

### Fixed fonts (always, regardless of combo)

| Element | Font |
|---------|------|
| Psychosis body text | Nimbus Roman |
| smooth1–4 wandering hooks | Termina |
| smooth5 escape link | DejaVu Sans |
| Title screen Start link | Termina |
| Title screen description | Logic Monospace |
| Title screen "Ryu Konrad" link | Termina |
| Title screen GitHub link | Nimbus Roman |
| Home button | Termina |
| Fullscreen button | Termina |
| Intrusion words | Nimbus Roman |

---

## 8. CRRF SYSTEM (Complete Random Reality Fragmentation)

A modular randomised passage system. Each CRRF passage assembles itself from 4 random pools:

```
(display: (either: "CRRF Background 1", "CRRF Background 2", ...))
(display: (either: "CRRF Image 1", "CRRF Image 2", ...))
(display: (either: "CRRF Text 1", "CRRF Text 2", ...))
(display: (either: "CRRF Links 1", "CRRF Links 2", "CRRF Links 3"))
```

**To add a new option to any pool:** create a new passage (e.g. `CRRF Text 5`) and add its name to the `(either:)` list.

**Current pools:**
- **Background 1–4:** full-screen overlay images (static.gif, parkpsychosis.gif, psychward.jpg, nightambience.jpg)
- **Image 1–4:** erratic-img (static.gif, nightambience.jpg [placeholder], parkpsychosis.gif, brokentoilet.jpg)
- **Text 1–4:** short placeholder text fragments
- **Links 1–3:** navigation options (currently all lead to Home / Psych Ward)

**Currently used on:** CRRF 1, 2, 3, 4 (four distinct passages using the same pool system).

---

## 9. LINK BEHAVIOUR

| State | Appearance |
|-------|-----------|
| Unvisited | White at 90% opacity, glow, fidget animation |
| Visited | 55% opacity, strikethrough, no animation |
| Hover | Colour flicker (red→cyan→yellow→magenta→white), stays fidgeting |
| Visited hover | No change (locked) |

Title screen links: no fidget animation (they're UI, not choices). Border box style.

A `[dev] toilet psychosis` placeholder link on the Title Screen links directly to Public Toilet Psychosis for testing. Remove when no longer needed.

---

## 10. QUICK RECIPE GUIDE

**New room with background + audio:**
```
:: Room Name [mytag breathe] {"position": "x,y", "size": "100,100"}
```
Add to `TAG_BACKGROUNDS`: `'mytag': './images/myimage.jpg'`  
Add to `TAG_TRACKS`: `'mytag': 'mytrack'`

**New solid-colour room:**
```
:: Room Name [blue breathe] {"position": "x,y", "size": "100,100"}
```
Add to `SOLID_COLORS` in `updateBackground()`: `'blue': '#0029a3'`  
Do NOT add a CSS `background-color` rule on `tw-story` for this tag — JS handles it.

**Random text in passage:**
```
(either: "option one", "option two", "option three")
```

**Wandering psychosis fragment:**
```
|smooth1>[(either: "text a", "text b")]
```

**Fake escape link:**
```
|smooth5>[(link-replace: (either: "leave", "run"))[(either: "nice try", "not yet")]]
```

**Auto-redirect after delay:**
```
(live: 20s)[(goto: (either: "Passage A", "Passage B"))]
```

**Dissolving/echo/contradict passage:**
Add tag `[dissolve]`, `[echo]`, or `[contradict]` to the passage header.

**Decor (floating background text):**
Create `:: My Passage decor [decor]` with the intrusion poem text.

**External link styled as game link:**
```html
<a class="game-link" href="https://..." target="_blank">link text</a>
```

**Layout randomiser — suppress for a passage (opt-out):**
Add `psychosis` or `titlescreen` tag. No per-passage disable otherwise — it always runs on normal passages.

---

## 11. BUILD SCRIPTS (`twine-twee-edit/`)

| Script | What it does |
|--------|-------------|
| `twee_to_html.py` | Compiles `story.twee` → `index.html` (also copies to `pathologisation/`). Auto-runs `twee_to_proof.py` at the end. |
| `html_to_twee.py` | Extracts `pathologisation/index.html` (Twine's save target) → `story.twee`. Run after editing in Twine. |
| `twee_to_proof.py` | Generates `pathologisation/proof.html` from `story.twee`. Run standalone or auto-called by compile. |

**Proof sections:**
1. **Linked** — passages reachable via the link graph from Title Screen, in BFS order
2. **Unlinked** — story passages not yet connected to the graph (works in progress, incomplete branches)
3. **Randomisation** — two sub-sections:
   - *text* — `[fragment-pool]` passages + `CRRF Text 1–4` + `CRRF Links 1–3`
   - *images* — `CRRF Image 1–4` + `CRRF Background 1–4`
   - Detection: CRRF sub-passages matched by name prefix; fragment-pool passages matched by tag
4. **Decor** — passages tagged `[decor]`
5. **System** — `hal.tracks`, `hal.config`; excluded: StoryTitle, StoryData, Story Stylesheet, Story JavaScript (same as Twine's own proof)

---

## 12. FRAGMENT POOL SYSTEM

Centralises all randomised text for psychosis rooms into dedicated passages, keeping the room passages clean and all content editable in one place.

### Pool passage format

Tag: `[fragment-pool]`. One line per key, options separated by `|`:

```
:: Psychosis Fragment Pool [fragment-pool]
body:    First body text option. | Second option. | Third option.
smooth1: What are you doing? | The fuck are you here for? | Are you ok?
smooth2: You look retarded. | Shit fuck. | Wanna punch on?
smooth3: Just like me. | Alone and afraid. | Needles. Government Psy-Op.
smooth4: Just like me. | Alone and afraid. | Needles. Government Psy-Op.
```

- **`body:`** — replaces the passage's body text before `applyPsychosisLayout` runs, so the injected text gets the full cummings scatter + typewriter treatment. If omitted, the passage's own text is used as fallback.
- **`smooth1:`–`smooth4:`** — each hook gets one randomly selected option injected after layout, before wandering begins. Each is independently editable.
- **`#`** at the start of a line = comment, ignored by the parser.
- `smooth5` is never touched by the pool — it has interactive Harlowe link markup and stays as written in the passage.

### Call order on room load

```
injectFragmentPoolBody(p, tags)   ← body: option replaces passage text
applyPsychosisLayout(p)           ← scatters whatever body text is there
injectFragmentPool(p, tags)       ← smooth1–4 get their random option
startPsychosisWander(p)           ← hooks begin moving
```

### Room-specific pools

Add a second pool passage with a tag matching the room's own tag:

```
:: Park Psychosis Fragment Pool [fragment-pool parkpsychosis]
body:    Park-specific body text. | Another park option.
smooth1: Park-specific fragment. | Another one.
...
```

JS picks the most specific match (shared tag with room), falls back to the generic pool if none found.

### Adding new smooth hooks

1. Add CSS for the new hook name (font, size, animation)
2. Add it to `startPsychosisWander` with desired movement behaviour
3. Add its name to the inject list in `injectFragmentPool`
4. Add a line in the pool passage: `smooth6: option | option`
5. Add `|smooth6>[ ]` to the psychosis passage
