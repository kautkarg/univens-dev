# Univens — Theme & System Design

> **How to use this doc:** Read it cover-to-cover before making any changes. It defines the immutable core of this project — the architecture, visual identity, scroll mechanics, and content structure. If you need to modify something, first check whether it's marked as a constraint or a preference. Constraints must be preserved. Preferences can be adjusted if the spirit of the theme is maintained.

---

## 1. Project Identity

**Univens** is an execution partner for growing businesses. The website speaks 1:1 to the visitor — a CEO having a direct, honest conversation with one person. No marketing fluff. No "we're a full-service agency" language. The brand is confident, minimal, and personal.

### Core principles
- **Conversational** — The visitor is being spoken to directly, not marketed at
- **Premium minimal** — White background, black text, Inter font, generous whitespace
- **Scroll-driven narrative** — Text stays fixed in viewport center; only opacity changes on scroll
- **No distractions** — No animations that move text, no parallax, no blur transitions on scroll

---

## 1a. Project Context & History

### Origin
The project started as a standard landing page for Univens, an execution partner. The original layout was inspired by conventional agency websites. The user rejected this direction — they didn't want a "general website" feel.

### The Charlie Osborne detour
The user initially referenced the Charlie Osborne website as a structural inspiration (fixed-stage scroll with opacity transitions). We built the first version using that pattern. The copy was rewritten from scratch in Univens' own CEO voice — personal, direct, 1:1 conversation. The user later decided to move away from the Charlie Osborne layout entirely, keeping only the fixed-stage scroll mechanic.

### Abandoned approaches (do NOT revisit)
| Approach | Why it was rejected |
|----------|-------------------|
| **Natural scroll layout** | User explicitly said this is the "core of our website experience" — removed text from viewport center. Was reverted immediately. |
| **Sticky-based section stacking** | Less reliable across browsers (Edge issues). Fixed-stage + spacer is the established, working approach. |
| **Blur/translateY on scroll** | Creates illusion of text movement. User wants pure opacity-only transitions. |
| **Image-based logo** | The `logo.png` file exists but wasn't rendering reliably in the nav. Replaced with text logo "Univens". |
| **Nav with step counter + progress bar** | Previous navbar had `01 / 10` step counter and a fill progress bar. User stripped it to just logo + CTA — minimal, always visible, no background bar. |
| **Marketing copy** | Original copy was standard agency language. Completely rewritten in CEO conversational voice. |

### Key decisions & rationale

| Decision | Rationale |
|----------|-----------|
| **60vh scroll budget per section** | Reduces scrolling fatigue vs. original 100vh. Feels natural and paced. |
| **18vh fade-in, 10vh fade-out** | Entering text should feel slow and premium; exiting text should get out of the way quickly. No overlap between sections. |
| **Smoothstep easing** (`t²(3-2t)`) | More organic than cubic/quart. Used for all scroll-based opacity transitions. |
| **Fixed-stage + spacer over position:sticky** | Works consistently across all browsers including Edge. The spacer creates predictable scroll distance. |
| **Text logo over image logo** | Reliable rendering, no broken image state, consistent with minimal theme. |
| **Narrow reading column (36em)** | Feels like a letter or editorial spread — intimate, personal, CEO-to-visitor. |
| **Word-by-word lede reveal** | Adds conversational rhythm — each opening thought reveals itself like someone speaking. |

### Copywriting approach
- Written from Univens' perspective (first-person plural "we")
- Directly addresses the visitor as "you" — 1:1 conversation
- No marketing buzzwords (no "innovation", "synergy", "digital transformation")
- Each section leads with a conversational hook (lede), then elaborates
- The tone is confident, honest, and slightly informal — like a real CEO conversation
- 10 sections with a natural narrative arc: problem → understanding → solution → close

### Current state
- All 10 sections are written and styled
- Scroll mechanics are working and tested
- Button design was recently redesigned to match the conversational theme
- Text transitions (word reveal + paragraph stagger) are active
- The page is a single HTML file with embedded CSS + JS
- No external dependencies (except Google Fonts for Inter)

### Next steps / open areas
- Fine-tuning transition timing and ease
- Potential canvas blob color shifts per section for mood variation
- Loading states or micro-interactions on the hero
- Any additional polish the user requests

---

## 2. Technical Architecture

### 2.1 Layout approach: Fixed-stage + spacer
This is the core architectural decision and must NOT be changed:

```
┌─────────────────────────────┐
│  .stage (position: fixed)   │  ← All text lives here, locked in viewport
│  ┌───────────────────────┐  │
│  │  .section-block × 10  │  │  ← Absolutely positioned, stacked, opacity-controlled
│  └───────────────────────┘  │
├─────────────────────────────┤
│  #spacer (height: 600vh)    │  ← Creates scroll distance, pushed footer down
├─────────────────────────────┤
│  footer                     │
└─────────────────────────────┘
```

**Why this works:** The `.stage` is `position: fixed` so its content never moves. The `#spacer` div (inline `height: 600vh` = 10 slides × 60vh budget) creates real scroll distance in the document flow. JavaScript maps `window.scrollY` through the spacer to compute which section is active and at what opacity.

**Critical CSS:**
```css
.stage{position:fixed;top:0;left:0;width:100%;height:100vh;overflow:hidden;z-index:2}
.section-block{position:absolute;inset:0;display:flex;align-items:center;padding:clamp(...);opacity:0;pointer-events:none}
#spacer{pointer-events:none}  /* height set via inline style="height:600vh" */
```

### 2.2 Scroll math

```javascript
const BUDGET = 60; // vh per section
// spacer height = TOTAL * BUDGET = 600vh

function updateSlides(){
  const maxScroll = spacer.offsetHeight - window.innerHeight;
  const progress = Math.min(1, window.scrollY / maxScroll);
  const rawIndex = progress * (TOTAL - 1); // TOTAL = 10

  sections.forEach((sec, i) => {
    const diff = rawIndex - i;
    let opacity;
    if (diff < -0.18) opacity = 0;                    // before entering
    else if (diff < 0) opacity = smoothFade((diff + 0.18) / 0.18);  // fade in (18vh)
    else if (diff < 0.85) opacity = 1;                // fully visible (~47vh)
    else if (diff < 0.95) opacity = 1 - smoothFade((diff - 0.85) / 0.1);  // fade out (10vh)
    else opacity = 0;                                  // after exiting
    sec.style.opacity = opacity;
    sec.classList.toggle('active', opacity > 0.5);    // triggers text reveal
  });
}
```

- **Fade in:** 18vh of scroll (slow, ~18% of budget)
- **Full visibility:** ~47vh of scroll
- **Fade out:** 10vh of scroll (fast, ~10% of budget)
- **Easing:** Smoothstep (`t²(3-2t)`) for a premium organic feel
- **Throttled** via `requestAnimationFrame` with a tick guard

---

## 3. Design System

### 3.1 Colors
| Token | Value | Usage |
|-------|-------|-------|
| Background | `#fff` | Page, stage, nav, footer |
| Text primary | `#000` | Headings, strong emphasis |
| Text body | `rgba(0,0,0,.62)` | Body paragraphs |
| Text lede | `rgba(0,0,0,.72)` | Opening thought of each section |
| Text subtle | `rgba(0,0,0,.35)` | Secondary CTA, nav logo, nav CTA |
| Text muted | `rgba(0,0,0,.2)` | Step numbers, card labels, captions |
| Selection | `#fff3a6` | Text selection highlight (yellow) |
| Border subtle | `rgba(0,0,0,.06)` | Dividers, card borders, nav line |
| Border light | `rgba(0,0,0,.1)` | Button outlines |
| Button hover bg | `#000` | Primary button fills black on hover |
| Canvas blobs | Warm tones `rgba(245,235,220, .18)` | Atmospheric background |

### 3.2 Typography
```
font-family: Inter, sans-serif (weights 300-700 loaded from Google Fonts)
```

| Element | Size | Weight | Line-height | Letter-spacing | Color |
|---------|------|--------|-------------|----------------|-------|
| h2 (hero) | `clamp(36px,4.2vw,66px)` | 400 | 1.06 | -.03em | `#000` |
| p.lede | `clamp(22px,1.6vw,30px)` | 450 | 1.35 | -.01em | `rgba(0,0,0,.72)` |
| p | `clamp(16px,1.1vw,20px)` | 400 | 1.6 | -.005em | `rgba(0,0,0,.62)` |
| p.quote | `clamp(22px,1.6vw,31px)` | 400 (italic) | 1.35 | -.005em | `rgba(0,0,0,.65)` |
| .strong | inherit | 500 | inherit | inherit | `#000` |
| nav logo | 12px | 500 | — | -.01em | `rgba(0,0,0,.25)` |
| nav CTA | 11px | 400 | — | 0 | `rgba(0,0,0,.2)` |

### 3.3 Spacing
- **Internal gaps (between paragraphs):** `clamp(20px,2.5vw,40px)` via `.gap` divs
- **Section padding:** `clamp(32px,4vw,64px)` top/bottom, `clamp(24px,5vw,80px)` left/right
- **Button row margin-top:** `clamp(24px,3vw,44px)`
- **Column width:** `.inner` = `max-width:36em` for single-column text sections

### 3.4 Layout variants per section

| Slide | Layout class | Description |
|-------|-------------|-------------|
| 0 (Hero) | `.lo-hero` | Two-column grid: text left (1.3fr) / buttons right (1fr, bottom-aligned). Collapses to 1fr on mobile. |
| 1 | (default) | Single column, centered vertically |
| 2 | `.sb-top` | Single column, top-aligned (padding-top: clamp(72px,8vw,120px)) |
| 3 | (default) | Single column, centered |
| 4 | (flex column) | Lede text top, work list below (gap: clamp(24px,3vw,44px)) |
| 5 | `.sb-bot` | Single column, bottom-aligned (padding-bottom: clamp(72px,8vw,120px)) |
| 6 | (flex column) | Lede text top, service grid below (gap: clamp(24px,3vw,44px)) |
| 7 | (default) | Stats centered (`.inner.tc`) |
| 8 | (default) | Single column, centered |
| 9 | (flex column) | Text centered top, buttons centered below (gap: clamp(32px,4vw,56px)) |

### 3.5 Buttons

**Primary (`.btn`):**
- Outlined: `1px solid rgba(0,0,0,.1)`, transparent background
- On hover: inverts to `background:#000; color:#fff; border-color:#000`
- Padding: `16px 32px`, font: `13px / 450`
- Smooth `0.4s` transition

**Secondary (`.btn-ot`):**
- Text only: no border, no background
- Low opacity text: `rgba(0,0,0,.35)`
- On hover: text darkens to `#000`, a `→` arrow fades in (opacity 0→1, translateX(-4px)→0)
- Padding: `16px 4px`

---

## 4. Component Inventory

### 4.1 Fixed elements (z-index order)

| Component | z-index | Description |
|-----------|---------|-------------|
| Canvas | 0 | 4 warm-tone radial gradient blobs with mouse parallax + scroll drift |
| Noise overlay | 1 | SVG film grain, `opacity:.02`, 1s steps animation |
| Stage (text) | 2 | Fixed container for all section content |
| Footer | 3 | Contact/social/legal, `position:relative;background:#fff` |
| Progress indicator | 99 | Right-edge vertical line with traveling dot, hidden on mobile |
| Nav | 100 | Logo (left) + Book a Call (right), always visible, pointer-events:none container |
| Cursor | 1080 | White 8px dot, `mix-blend-mode:difference`, lerp following, hidden on tablet/mobile |

### 4.2 Content sections (10 slides)

Each section is 60vh of scroll budget. Content is conversational, written in Univens' CEO voice.

| # | Lede (opening thought) | Key content | Extras |
|---|----------------------|-------------|--------|
| 0 | (hero "Hey.") | Intro paragraphs | 2 buttons |
| 1 | "Before we go any further…" | What we're not, what we are | — |
| 2 | "You're probably wondering what that actually means." | The fragmentation problem | — |
| 3 | "So we do something differently." | Services vs execution | — |
| 4 | "What does execution look like for us?" | 3 work categories | Work list |
| 5 | "Another question you're probably asking…" | Understanding your business | Quote block |
| 6 | "How we make that happen" | 4-step process | Service grid |
| 7 | — | Stats: 1 partner, → execution, 6 areas | Stats row |
| 8 | "If you've read this far…" | Closing argument | — |
| 9 | "Whenever you're ready, we're here." | Final CTA | 2 buttons |

### 4.3 Navbar
```
Logo ("Univens")           (spacer)           "Book a Call"
```
- Always visible, no hide/show on scroll
- `pointer-events:none` on the nav itself, `pointer-events:auto` on clickable children
- Logo: 12px / weight 500 / opacity .25 → full on hover
- CTA: 11px / weight 400 / opacity .2 → full on hover

### 4.4 Footer
4-column grid (collapses to 2 on mobile):
1. Brand description
2. Contact (email, phone)
3. Social (LinkedIn, Twitter/X)
4. Legal (Privacy, Terms)
Bottom bar: copyright + "Execution partner"

---

## 5. Animation System

### 5.1 Text reveals (triggered by `.active` class)

**Word-by-word blur-in (`.lede` paragraphs):**
- On page load, each `.lede` paragraph's text is split into individual `<span class="ch">` elements
- Each `.ch` starts with `opacity:0; filter:blur(10px)`
- When parent `.section-block` gets `.active` class (opacity > 0.5), words transition to `opacity:1; filter:blur(0)`
- Duration: `1.2s` with cubic-bezier(.22,1,.36,1)
- Stagger: `120ms` between each word (set via inline `transition-delay`)
- Once `.active` is added, it stays (words remain visible even as section fades out)

**Paragraph stagger (body text):**
- `.active .inner > p:not(.lede)` fades in via `fpi` keyframe animation
- Duration: `1s`, staggered at `250ms` intervals per paragraph index
- Uses `both` fill mode to hold final state

### 5.2 Hero intro
- The word "Hey." splits into word spans with `.wrd` class
- Uses CSS `@keyframes bi` — blur(14px)→blur(0) over 1s
- Each word delayed by `90ms`, first at `350ms`

### 5.3 Canvas animation
- 4 blobs, each with: position, radius (150-400px), drift velocity, color, parallax ratio, scroll ratio
- Drifts continuously, wraps around edges
- Mouse parallax: blob position shifts by `mm.x * pr * 30`
- Scroll drift: blob position shifts by `scrollY * sr * 0.04`
- Runs at 60fps via `requestAnimationFrame`

### 5.4 Cursor
- Lerps toward mouse with `0.12` factor
- White 8px dot with `mix-blend-mode:difference`
- Hidden on screens ≤ 1023px

---

## 6. Background layers (rendering order, bottom to top)

1. **Canvas** (`#bg-canvas`, `z-index:0`) — warm gradient blobs, interactive
2. **Noise** (`#noise`, `z-index:1`) — SVG film grain at 2% opacity, steps animation for noise texture
3. **Stage** (`z-index:2`) — all text content
4. **Progress** (`.prg`, `z-index:99`) — right edge scroll indicator
5. **Nav** (`z-index:100`) — logo + CTA
6. **Cursor** (`z-index:1080`) — custom pointer

---

## 7. Constraints (must NOT be changed)

- **No position:fixed alternative** — Must use `.stage` (fixed) + `#spacer` pattern
- **No blur/translateY/transform on scroll transitions** — Section-level opacity only
- **No text movement on scroll** — Only opacity fades between sections
- **Sections must not visibly overlap** — Only one section's text visible at any scroll position
- **Fixed-stage architecture is required** — Do not switch to natural scroll or sticky-based approaches
- **Single HTML file** — All CSS and JS must remain embedded
- **Inter font** — Must be loaded from Google Fonts
- **White background** — `#fff`, dark text `#000`
- **Selection color** — `#fff3a6`
- **Custom cursor** — Must have `mix-blend-mode:difference`, hidden on mobile
- **Canvas with mouse parallax** — Must be present

---

## 8. Preferences (can be adjusted with care)

- Button style can evolve as long as it feels conversational
- Typography sizes can be adjusted if the premium feel is maintained
- Section alignment variants (`.sb-top`, `.sb-bot`) can be reassigned
- Text reveal timing (word stagger, paragraph delay) can be tuned
- Progress indicator style can change
- Canvas blob colors can shift per section for mood variation

---

## 9. File structure

```
Test 1/
├── index.html    # Single-file page (~366 lines)
│   ├── <head>    # Fonts, styles
│   ├── <body>    # Canvas, cursor, noise, nav, progress, stage, spacer, footer, script
│   └── <script>  # Canvas render, cursor lerp, hero split, lede split, scroll math
├── theme.md      # This document
└── logo.png      # (exists but unused; using text logo instead)
```

---

## 10. Common pitfalls for AI agents

1. **Don't remove the fixed-stage pattern** — It's the core UX. Natural scroll breaks the "text stays fixed" constraint.
2. **Don't change `.spacer` class to something else** — It conflicts with internal `.gap` divs. The main spacer uses `id="spacer"` with inline `height:600vh`. Internal paragraph spacers use `.gap` class.
3. **The `active` class is additive** — Once added, never removed. This ensures word reveals don't blur back out when a section fades away.
4. **Canvas blobs use `scrollY` not `window.scrollY`** — The `sy` variable is updated by a dedicated scroll listener and used in the animation loop.
5. **Button inline styles removed** — Center alignment is now in the CSS `.btn` rule.
6. **Smoothstep is `t²(3-2t)`** — Not cubic bezier. Used for section fade transitions.

---

## 11. Cross-device workflow

Since opencode does not persist conversation history between machines, `theme.md` serves as the project's long-term memory. Follow this workflow to maintain continuity:

### For the human
1. **Before committing/pushing**, verify that `theme.md` reflects the latest state of the project
2. If you made decisions or requests that aren't captured, tell the AI to update `theme.md` before closing the session
3. Commit and push both `index.html` and `theme.md`

### For the AI agent (on a new machine)
1. **Start by reading `theme.md`** — it contains the full project context, architecture, design system, constraints, and session history
2. Check the **Session Log** below to understand the most recent changes and the current state
3. Read `index.html` to see the live code
4. Proceed with the task, and **append to the Session Log** when you make changes

---

## 12. Session Log

Append entries here at the end of each work session. Each entry should capture what was done and why.

---

### Session 1 — Initial setup & architectural foundation
- Built the single-file `index.html` with fixed-stage + spacer scroll architecture
- Wrote all 10 sections of conversational CEO copy
- Implemented scroll-driven opacity with smoothstep easing
- Added atmospheric canvas (4 warm blobs, mouse parallax, scroll drift)
- Added custom cursor (white dot, mix-blend-mode: difference)
- Added SVG film grain noise overlay at 2%
- Built minimal navbar (logo + step counter + CTA + progress bar, initially hidden, appears on scroll)
- Added footer with contact, social, legal links
- Set up Inter font, clamp-based fluid sizing, white/black color scheme

### Session 2 — Nav redesign & layout revamp
- Changed logo from image (`logo.png`) to text ("Univens") for reliability
- Stripped nav to ultra-minimal: logo (left) + "Book a Call" (right), always visible, no background, no step counter, no progress bar
- Added vertical progress indicator on right edge (thin line + traveling dot, hidden on mobile)
- Removed fixed-stage + spacer → switched to natural scroll layout (user later rejected this)

### Session 3 — Restored fixed-stage architecture
- Reverted natural scroll → restored fixed-stage + spacer as the core UX
- Kept visual improvements from the revamp: narrow editorial column (36em), progress dot, minimal nav, improved typography
- Fixed spacer height issue: moved from JS-set height to inline `style="height:600vh"` to eliminate CSS class conflicts
- Separated internal paragraph spacers (`.gap`) from main scroll spacer (`#spacer`)
- Upgraded typography: larger body text (16-20px), darker colors (`.62` opacity), bolder ledes (450 weight, 22-30px, `.72` opacity)
- Added `.quote` class for emphasized pull-quotes with border frames
- Increased spacing and column width for editorial feel

### Session 4 — Text transitions & button redesign
- Added word-by-word blur reveal on lede text (`.ch` class, 1.2s transition, 120ms stagger)
- Added paragraph stagger animation for body text (1s fade-in, 250ms intervals)
- Active class added on scroll (additive, never removed)
- Section alignment variants: `.sb-top` (slide 2, higher), `.sb-bot` (slide 5, lower)
- Added hover micro-interactions on work items and service cards
- Added smooth body entrance animation
- Redesigned buttons: primary (outlined → fills black on hover), secondary (text-only with → arrow reveal)
- Created this `theme.md` document with full system design, constraints, and context

### Session 5 — Bidirectional text transitions
- Fixed text transitions only firing on first scroll (not on scroll-back)
- Changed `.active` class from additive (`if(o>.5)sec.classList.add('active')`) to toggled (`sec.classList.toggle('active', o>.5)`) so sections lose `.active` when they fade out
- Switched paragraph stagger from CSS `@keyframes fpi` animation to CSS `transition` on `opacity` — animations only fire once even with class removal/re-add, transitions replay bidirectionally
- Added `opacity:0; transition:opacity 1s ...` to base `.section-block .inner > p:not(.lede)` rule so paragraphs fade back out when `.active` is removed
- Removed `@keyframes fpi` (no longer needed)
- `.ch` word-reveal transitions already used CSS transitions, so they work bidirectionally with no change

### Session 6 — Heading-first content reveal
- Shifted all body paragraph `transition-delay` by +2s (first paragraph starts at 2s, stagger continues at 2.25s, 2.5s, etc.) so `.lede` word-reveal completes before body content appears
- Added `opacity:0; transition: opacity 1s ...` with delayed `.active` reveal to content blocks: `.quote` (2s), `.lr` buttons (2.5s), `.wrk-list` (2.5s), `.srv-grid` (2.5s), `.stat-row` (2.5s)
- Result: on section enter, `.lede` words blur in immediately; after ~2s, body paragraphs and content blocks fade in with staggered timing

### Session 7 — Enhanced progress indicator with section context
- Replaced the 1px × 120px subtle progress bar with a more prominent 2px × `min(55vh,360px)` bar
- Added 10 section markers (small dots) at each section's progress position along the bar
- Active section marker enlarges and darkens (4px→6px, opacity .08→.28)
- Added section number label (`01`–`10`) positioned to the left of the bar, updates on scroll
- Kept the traveling dot for precise position feedback
- All elements hidden on mobile (≤768px) as before

### Session 8 — Cursor redesign (gap-ring system)
- Completely redesigned the cursor around the concept of "completion" — the ring has a gap that gradually closes as you scroll through the narrative, fully closes when ready to click, and breathes when idle
- **Dot** (6px, dark gray `rgba(0,0,0,.55)`) — tracks exact mouse position, the "point of execution"
- **Gap-ring** (38px) — rendered via `conic-gradient` + `mask` for a dynamic arc
- **Scroll-responsive gap**: gap starts at 30° at the top of the page and linearly decreases to 0° as `scrollProgress` reaches 1 (the CTA section). The cursor's incompletion mirrors the narrative's incompletion — as the story resolves, the ring completes
- **Hover completion**: gap closes to 0° over interactive elements (`a`, `.btn`, `.cta`, `button`, `.wrk-item`, `.srv-card`) — visual metaphor for "ready to execute"
- **Gap lerp**: current gap smoothly interpolates toward target gap at 0.08 factor, avoiding visual jumps between states
- **Idle breath**: after 4s without mouse movement, the ring subtly pulses (±1.5% scale at 0.0015 frequency) — feels like a quiet breath, makes the page feel alive and listening
- **Click snap**: ring contracts to 0.7× on mousedown, snaps back on release
- **Default cursor hidden** via `body { cursor: none }` on desktop (≥1024px)
- Hidden entirely on touch/tablet (≤1023px)
- Removed `mix-blend-mode: difference` — uses explicit dark colors on white background for reliable cross-browser rendering
- **Critical fix**: `scrollProgress` was declared with `let` after the cursor code, causing a Temporal Dead Zone `ReferenceError` on first `cur()` call — halted all JS execution including `updateSlides()`, making all content invisible. Moved `scrollProgress` before the cursor code to resolve.

### Session 9 — Value-driven cursor features
- Added **contextual whisper labels**: when hovering over interactive elements, a tiny label fades in 24px to the right of the ring, communicating what the action means: "Book a Call" on primary CTA, "Our process →" on secondary CTA, work item titles on `.wrk-item`, service names on `.srv-card`, link text on `<a>` tags. This makes the cursor feel like a silent assistant that explains each action.
- Added **element-type-aware ring response**: primary buttons/CTA contract ring to 0.78× (decisive), secondary buttons to 0.85× (exploratory), work items/service cards to 0.9× (inviting to explore), regular links to 0.82×. Each conveys the nature of the interaction.
- Added **section-type resonance**: ring subtly adjusts scale based on narrative arc — problem sections (0.97×, slight tension), solution sections (1.02×, openness), closing/CTA sections (1.03×, warmth). Orientates the user within the story.
- Added **scroll velocity fade**: when scrolling fast (>0.8 px/ms, within 120ms of last scroll event), the ring fades proportionally down to 0.15 opacity. Rewards slow, deliberate reading — aligns with the "conversation, not skimming" ethos.
- Improved hover detection: tracks the actual hovered element (`hoverEl`) instead of a boolean, enabling type-specific behavior. Added `.btn-ot` to the selector list.
- Label positioned via CSS `left`/`top` (no transform conflict) with `transition: opacity .25s` for smooth fade in/out.

### Session 10 — Cursor state machine (magnetic, stretch, breath, blink, particles)
- Full cursor JS restructured as a **state machine** — each frame evaluates independent state inputs (magnetism, curiosity, transition, breathing, blink, scroll velocity) to compose a final ring transform. No external state library; all logic in the `cur()` animation loop.
- Added **magnetic attraction**: interactive element positions cached via `getBoundingClientRect()` on load/scroll/resize. When cursor is within 60px of the nearest element (and not already hovering it), a gravitational pull (up to 16px offset) tugs the ring toward the element's center. Creates a "gravity well" feeling — the ring leans toward interactive targets before the user reaches them.
- Added **curiosity stretch**: ring deforms along the velocity vector when mouse moves fast (>2px/frame). Stretch factor up to 1.08× in movement direction, 0.92× perpendicular. Smoothly lerped via `curSx`/`curSy` (0.12 factor) to avoid jitter. Makes the cursor feel wind-resistant, like it has mass.
- Replaced simple idle sine wave with **continuous organic breathing** (`Math.sin(now*0.0013)*0.006`, ~4.8s cycle) — subtle constant oscillation rather than starting/stopping after idle.
- Added **resting blink**: after 20s of inactivity, the dot's scale Y pinches toward 0 whenever `Math.sin(now*0.004)` crests above 0.85. Simulates a slow, restful eye blink. The dot "closes" momentarily, reinforcing the cursor as a living presence.
- Added **section transition pulse**: tracks `lastSec` on every frame; when `scrollProgress*9` rounds to a new section index, triggers a 300ms pulse (1.08× scale up-down via `Math.sin(transAge*PI)`). Brief "breath" when crossing narrative boundaries — feels like the cursor is aware of the story structure.
- Added **text reading mode**: ring gap opens +8° when hovering over a `<p>` element (`hoverEl.tagName==='P'`). Subtle invitation to read; reverts immediately on exit.
- Added **scroll particle trail**: on every scroll event while the cursor is active, spawns a 3px dot at the ring's position. Particles fade to 0 over 500ms, then are recycled into a pool (`pPool`) to avoid DOM thrash. Only visible during scroll — suggests movement through space.
- All features compose multiplicatively in the `s` scale variable — no conflicts between breathing, transitioning, element-type response, section resonance, or click-snap.

---

## 13. Cinematic Experience System

### Philosophy

Visitors should never feel like they are scrolling through a website. They should feel like they are progressing through a story.

- Every viewport is a scene.
- Every scroll advances the narrative.
- Every interaction builds anticipation.
- Every transition has intention.

### The Five Rules

1. **Never reveal everything immediately** — Every viewport creates one unanswered question. Curiosity is stronger than explanation.
2. **One hero per viewport** — One sentence. One interaction. One visual. Never compete for attention.
3. **Motion follows emotion** — Animation is never decorative. Everything moves because the story needs it.
4. **Silence is part of communication** — Whitespace creates anticipation. Let the visitor breathe.
5. **Reward curiosity** — Every scroll reveals something new. Never make scrolling feel repetitive.

### Cinematic Timeline (3 Acts)

**Act 1** — Arrival → Curiosity → Recognition  
**Act 2** — Trust → Understanding → Connection  
**Act 3** — Confidence → Relationship → Action

Emotional intensity gradually increases. Never peaks too early.

### Scene Composition

Every viewport has four layers:
- **Foreground**: Conversation (the copy)
- **Middle Layer**: Visual Explanation (illustration, canvas, logos, cards)
- **Background**: Ambient Motion (canvas blobs, noise, glow)
- **Cursor**: Interaction Layer (brand state machine)

### Camera Principles

The website behaves like a camera — it chooses where visitors should look:
- Typography scales
- Illustration stays
- Whitespace changes
- Perspective shifts

Only one visual element should dominate each viewport. Never two heroes.

### 4-Phase Scroll Choreography

Each viewport has four phases based on local scroll progress (0–1):

| Phase | Progress | What happens |
|---|---|---|
| **Arrival** | 0.00–0.12 | Everything still, no distractions, visitor observes |
| **Discovery** | 0.12–0.45 | Primary content appears — one idea, nothing else |
| **Development** | 0.45–0.78 | Interaction begins, illustration evolves, cursor reacts |
| **Transition** | 0.78–1.00 | Current scene slowly exits, next scene begins entering |

Scenes overlap — the visitor should never experience an empty transition.

### Layers within each viewport

Content is assigned to layers: `.phase-a` (Discovery), `.phase-b` (Development). Layer visibility is controlled by `data-phase` attribute set by the scroll engine based on local progress. CSS transitions handle the animation.

### Emotional Color Mood

| Section | Mood |
|---|---|
| Welcome | Neutral |
| Recognition | Slight warmth |
| Trust | Bright whites |
| Understanding | Cool greys |
| Connection | Soft accent colours |
| Action | Highest contrast |

### Cinematic Timing

| Purpose | Duration |
|---|---|
| Simple reveals | 250ms |
| Reading transitions | 450ms |
| Scene transitions | 800ms |
| Major reveals | 1200ms |
| Moments of reflection | 1500ms |

### Scroll Velocity Adaptation

- **Slow scroll** (<0.2 px/ms): Reveal details, allow animations to complete, encourage exploration
- **Fast scroll** (>0.5 px/ms): Skip layer phase delays, reveal all layers immediately, preserve flow

The interface adapts to the visitor's pace.

### Scene Exit

Every scene leaves something behind — a breadcrumb, a phrase, a selected choice, a visual motif. The website remembers. The visitor feels continuity.

---

## 14. Director's Briefs

### V1 — The First Impression
| Field | Value |
|---|---|
| **Narrative Purpose** | Make the visitor stop. Not by shouting — by understanding them. |
| **Primary Emotion** | Curiosity, calm |
| **Visual Metaphor** | Empty stage, curtain about to rise |
| **Camera Behavior** | Locked, very slow zoom into whitespace |
| **Pacing** | Slow, contemplative |
| **Sound Analogy** | Whisper — the room is quiet, someone is about to speak |
| **Exit Feeling** | Intrigued — "What is this?" |

### V2 — The Moment of Engagement
| Field | Value |
|---|---|
| **Narrative Purpose** | Involve the visitor. Make them participate, not just read. |
| **Primary Emotion** | Curiosity → Recognition |
| **Visual Metaphor** | Crossroads, three paths diverging |
| **Camera Behavior** | Track — gentle push toward the choices |
| **Pacing** | Contemplative, patient |
| **Sound Analogy** | Soft heartbeat — anticipation |
| **Exit Feeling** | Heard — "They're asking me, not telling me." |

### V3 — The Problem Recognized
| Field | Value |
|---|---|
| **Narrative Purpose** | Show understanding. Describe the problem without saying "we understand." |
| **Primary Emotion** | Recognition |
| **Visual Metaphor** | Constellation connecting — isolated nodes finding each other |
| **Camera Behavior** | Parallax drift — camera pulls back as nodes connect |
| **Pacing** | Slow, meditative |
| **Sound Analogy** | Quiet realization — the moment pieces click together |
| **Exit Feeling** | Understood — "That's exactly how it feels." |

### V4 — The Proof
| Field | Value |
|---|---|
| **Narrative Purpose** | Build credibility without talking about ourselves. |
| **Primary Emotion** | Trust |
| **Visual Metaphor** | Trophy wall — logos as evidence |
| **Camera Behavior** | Pan across — slow horizontal reveal |
| **Pacing** | Steady, confident |
| **Sound Analogy** | Distant applause — quiet acknowledgment |
| **Exit Feeling** | Assured — "They've done this before." |

### V5 — The Offer
| Field | Value |
|---|---|
| **Narrative Purpose** | Explain what Univens actually is — center the visitor's business. |
| **Primary Emotion** | Understanding |
| **Visual Metaphor** | Solar system — the visitor's business at the center |
| **Camera Behavior** | Orbit — pull focus to center, then float around |
| **Pacing** | Calm, open |
| **Sound Analogy** | Clear tone — a bell ringing |
| **Exit Feeling** | Clarity — "I see what they do." |

### V6 — The Path
| Field | Value |
|---|---|
| **Narrative Purpose** | Explain the process without paragraphs — make it feel inevitable. |
| **Primary Emotion** | Trust |
| **Visual Metaphor** | Bridge being built — step by step |
| **Camera Behavior** | Track forward — moving through each phase |
| **Pacing** | Energetic, forward-moving |
| **Sound Analogy** | Steady rhythm — footsteps on a path |
| **Exit Feeling** | Confident — "That makes sense." |

### V7 — The Evidence
| Field | Value |
|---|---|
| **Narrative Purpose** | Replace testimonials with authentic evidence. |
| **Primary Emotion** | Connection |
| **Visual Metaphor** | Open scrapbook — real conversations, real moments |
| **Camera Behavior** | Zoom into details — close on each snippet |
| **Pacing** | Slow, intimate |
| **Sound Analogy** | Warm conversation — a friend telling a story |
| **Exit Feeling** | Connected — "This feels real." |

### V8 — The Truth
| Field | Value |
|---|---|
| **Narrative Purpose** | Handle the pricing objection before it's asked. |
| **Primary Emotion** | Relief |
| **Visual Metaphor** | Two doors — wrong way vs right way |
| **Camera Behavior** | Locked, direct — no distractions |
| **Pacing** | Quick, honest, direct |
| **Sound Analogy** | Deep breath — honesty creates safety |
| **Exit Feeling** | Relieved — "They get it." |

### V9 — The Vision
| Field | Value |
|---|---|
| **Narrative Purpose** | Sell peace of mind, not services. |
| **Primary Emotion** | Anticipation, hope |
| **Visual Metaphor** | Empty room — silence creates impact |
| **Camera Behavior** | Slow zoom in — the words fill the frame |
| **Pacing** | Very slow, reflective |
| **Sound Analogy** | Silence — then a single piano note |
| **Exit Feeling** | Hopeful — "I want that." |

### V10 — The People
| Field | Value |
|---|---|
| **Narrative Purpose** | Humanize Univens — show the people behind the work. |
| **Primary Emotion** | Trust, warmth |
| **Visual Metaphor** | Open notebook — sketches, ideas, coffee |
| **Camera Behavior** | Static, warm — like sitting across a table |
| **Pacing** | Calm, grounding |
| **Sound Analogy** | Quiet corner of a coffee shop |
| **Exit Feeling** | Welcomed — "I'd like to meet them." |

### V11 — The Invitation
| Field | Value |
|---|---|
| **Narrative Purpose** | Start the relationship, not capture a lead. |
| **Primary Emotion** | Anticipation, readiness |
| **Visual Metaphor** | Open door — threshold to cross |
| **Camera Behavior** | Push in — moving through the door |
| **Pacing** | Building — emotional crescendo |
| **Sound Analogy** | Orchestra build — final movement |
| **Exit Feeling** | Ready — "I know what to do next." |

---

## 15. Session Log

Append entries here at the end of each work session. Each entry should capture what was done and why.

### Session 11 — Full blueprint implementation & cinematic experience system
- **Complete rewrite**: Replaced all 10 sections with 11 viewports matching the Experience Blueprint. New copy, new interactions, new emotional arc.
- **Architecture scaled**: spacer from 600vh→660vh, progress indicator to 11 markers, scroll engine for 11 sections.
- **Hero word-fade**: Replaced character-by-character typing with word-by-word fade-in (opacity + translateY). Added ambient radial glow that follows mouse, cursor read-along (ring gap closes on each new line), punchline pulse (ring scales to 1.08× on "Neither are we."), and smart scroll hint (leans toward cursor in bottom 25% viewport).
- **V2 Choices**: Three floating options with hover lift and click response. Click changes copy dynamically.
- **V3 Nodes**: Dedicated canvas with 12 drifting nodes. Connection lines form as scroll progress increases (threshold tied to `vpProgress[2]`).
- **V4 Logo wall**: 8 brand names (Disney, Amazon, Nickelodeon, Viacom18, Sony, Netflix, Spotify, Stripe) with staggered scroll reveal via `reveal` class.
- **V5 Gravity**: "Your Business" at center with 8 floating service words (Website, Brand, Automation, AI, etc.) using spring-physics mouse attraction. Words drift back to orbital positions via damped springs.
- **V6 Timeline**: 5-step horizontal journey (You talk → We understand → We simplify → We execute → We improve). Hover reveals step descriptions.
- **V7 Conversation snippets**: 5 styled cards (Slack, Zoom, WhatsApp, email, planning notes) with blurred sensitive content and platform-colored left borders.
- **V8 Wrong Hire flow**: Arrow-connected comparison (Wrong Hire → Delays → Rework → Start with us instead).
- **V9 Poster typography**: 8 staggered poster lines with per-line transition delays (0.5s–2.5s). "Imagine not chasing updates anymore..."
- **V10 Workspace scene**: 6 positioned items (Notebook, Laptop, Coffee, Sketches, Project notes, Wireframes) with rotation.
- **V11 Input field**: Centered input with placeholder cycling (3 messages at 4s intervals). Enter-to-send note + CTA button.
- **Cursor upgrades**: Spring physics (SK=0.12, SD=0.68) replaces lerp. Arrow morph on links/buttons via clip-path triangle. Pencil morph on input focus (2×18px bar + dot shrink). Fixed `setRingMorph` class management to preserve `.ring` base class.
- **Body visibility**: Removed `opacity:0` body fade animation. Reduced typing delay from 500ms→200ms. Added zero-width space for initial div height.
- **Error isolation**: All IIFEs and main script sections wrapped in `try{...}catch(e){}` — a crash in one viewport can't break scroll engine or other viewports.
- **Nodes canvas**: Removed accumulating `devicePixelRatio` scale in `resizeNC()` that caused drawing to go off-screen.
- **Added Cinematic Experience System** to docs (Section 13): 5 rules, 3-act timeline, 4-phase scroll choreography, scene composition layers, camera principles, emotional color mapping, cinematic timing table, scroll velocity adaptation guidelines.
- **Added Director's Briefs** (Section 14): Scene title, narrative purpose, primary emotion, visual metaphor, camera behavior, pacing, sound analogy, exit feeling for all 11 viewports.
- **Implemented 4-phase scroll choreography**: Per-section `data-phase` attribute (arrival → discovery → dev → full) set by scroll engine based on local progress. Phase thresholds skip to 'full' during fast scroll (>0.5 px/ms) for velocity adaptation.
- **Added phase layers to CSS**: `.phase-a` (appears at discovery, 12% local progress) and `.phase-b` (appears at development, 45% local progress) with 0.7s cubic-bezier transitions. Both visible in 'dev' and 'full' phases.
- **Assigned phase classes** to all 11 viewports: primary content (lede, main interaction) gets `.phase-a`, secondary content (visuals, grids, supporting elements) gets `.phase-b`. Ambient elements (glow, canvas) remain unphased for always-on background.
- **Direct smooth snap scroll**: Replaced continuous scroll with section-by-section snapping. Wheel event triggers `snapTo()` which animates `window.scrollTo` with easeInOutQuad over 550ms. Scroll events during animation are prevented via `snapLock` flag. Keyboard navigation added (ArrowDown/Up, PageDown/Up, Space).
- **Spacer resized** from 660vh to 1100vh (11 × 100vh) — each section gets a full viewport of scroll distance for clean snap positioning.
- **Scroll throttling** refactored from `tick` flag to `schedU()`/`pendingUpdate` pattern for cleaner integration with snap animation.
- During snap, `updateSlides()` is called directly in the rAF loop for frame-accurate section opacity transitions. The existing crossfade window (~18% fade-in, ~13% fade-out overlap) creates a brief, smooth blend during the 550ms snap.
- **Modern typographic refinements**: Introduced CSS custom properties for a warm, sophisticated palette (`--text`, `--text-soft`, `--text-muted`, `--border`, `--accent`, `--glow`, `--warm`). Replaced `#fff` background with warm off-white `#faf8f5`. Refined type scale: body at `16–19px` / line-height 1.75, lede at `21–28px` / weight 300, hero at `30–54px` / weight 300 with tighter letter-spacing. Added `.sub` utility for small uppercase labels. Updated poster lines to weight 300 with 1.25 line-height. Refined all component colors (choices, logos, gravity words, timeline, conv cards, hire steps, workspace items, input, buttons, footer) to use palette variables. Updated all `.inner` transitions to `cubic-bezier(.22,1,.36,1)`. Added smooth `opacity .7s` transition on section blocks. Reduced noise opacity to 1.5%. Changed selection color to warm accent. Refined CTA button with hover pill background.
- **Implemented Chapter 15 Typography Motion Language**: Full rewrite of text animation system to match conversational, calm, intentional motion philosophy.
  - **Hero sentence reveal**: Replaced word-by-word animation with sentence-by-sentence reveal. First two sentences use Type 01 (typing), third uses Type 02 (Soft Fade Up), timing follows the chapter's table.
  - **Soft Fade Up (Type 02)**: New `.s-reveal` / `.s-inner` CSS system: opacity 0→1, translateY 12px→0, 700ms, spring easing.
  - **Context Memory**: `#ctx-memory` fixed element showing previous section's key sentence for conversational continuity.
  - **Removed `.hero-word`** CSS (no longer used) and all word-by-word animation logic.
- **Conversation V3 rewrite (Session 13)**: Full 11-viewport content replacement with new copy, interactions, and flow matching the new conversation blueprint.
- **Chapter 02 Design Concept alignment (Session 13)**:
  - **Removed ALL borders/containers** from interactive elements — choices, philosophy words, credibility logos, and execution loop steps are now pure text with color transitions only. Whitespace replaces containers.
  - **Nodes canvas made persistent**: Moved from inside V3 section to `position:fixed` background layer. Connection threshold scales with `scrollProgress * 1.5` — everything begins disconnected, scrolling creates order.
  - **Section type map updated**: `ST` array now maps to new V3 section names. Context memory messages, cache targets, and all JS references updated.
