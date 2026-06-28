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
