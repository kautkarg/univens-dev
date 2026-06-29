# Univens — Theme & System Design

> **How to use this doc:** Read it cover-to-cover before making any changes. It defines the immutable core of this project — the architecture, visual identity, scroll mechanics, and content structure. If you need to modify something, first check whether it's marked as a constraint or a preference. Constraints must be preserved. Preferences can be adjusted if the spirit of the theme is maintained.

---

## 1. Project Identity

**Univens** is an execution partner for growing businesses. The website speaks 1:1 to the visitor — a direct, honest conversation. No marketing fluff. No "full-service agency" language. The brand is confident, minimal, and personal.

### Core principles
- **Conversational** — The visitor is being spoken to directly, not marketed at
- **Micro-chat narration** — Every section starts with a chat bubble from "Univens" (avatar + label + bubble), creating a 1:1 conversation feel
- **Premium minimal** — White background, dark text, Inter Tight font, generous whitespace
- **Scroll-driven narrative** — Stage stays fixed; opacity and `.reveal` class handle transitions
- **70% visual / 30% copy** — Headings introduce the topic, interactive UI components carry the storytelling
- **No distractions** — No decorative animations, no glassmorphism, no shadows, no card containers (whitespace replaces borders)

---

## 1a. Project Context & History

### Origin
The project started as a standard landing page for Univens. The original layout was inspired by conventional agency websites. The user rejected this direction.

### The Charlie Osborne detour
The user initially referenced the Charlie Osborne website as a structural inspiration (fixed-stage scroll with opacity transitions). We built the first version using that pattern. The copy was rewritten in Univens' own CEO voice. The user later moved away from the Charlie Osborne layout, keeping only the fixed-stage scroll mechanic.

### V2.0 → V6.0 → v1.2 evolution
The site went through a fundamental restructuring:
- **V2.0**: 10 viewports with CSS-based text reveal system (abandoned)
- **V6.0**: Pivot to communication system, reduced to 7 viewports (abandoned)
- **v1.2** (current): 9-viewport "UI-first" architecture with JS-driven `.reveal` class transitions, 30-70 split layouts, chat-style narration, and custom interactive components

### Abandoned approaches (do NOT revisit)
| Approach | Why it was rejected |
|----------|-------------------|
| **Natural scroll layout** | User explicitly rejected moving text from viewport center |
| **Sticky-based section stacking** | Less reliable across browsers (Edge issues). Fixed-stage + spacer is the established approach. |
| **Blur/translateY on scroll** | Creates illusion of text movement. User wants pure opacity transitions. |
| **Image-based logo** | The `logo.png` existed but wasn't rendering reliably. Replaced with text logo + star icon. |
| **Nav with step counter + progress bar** | Previous navbar had step counters and progress bars. Stripped to just logo + CTA + hamburger. |
| **Marketing copy** | Original copy was standard agency language. Completely rewritten in CEO conversational voice. |
| **CSS phase system** | Old system used `.level-01`–`.level-05` CSS classes with opacity transitions controlled by `data-phase` attributes. Replaced with JS-driven `.reveal` class for reliable visibility control. |
| **Ecosystem/Network visualizations** | Custom canvas-based visualizations (eco-viz, net-viz) were removed in v1.2 in favor of simpler grid-based UI components. |
| **Canvas blobs** | Background canvas with warm gradient blobs was removed per Chapter 18 compliance (no decorative elements). |
| **30-70 split layout** | Split layouts (narration left, UI right) replaced by unified vertical narrative flow (Topic → Sentence → Visual → Support → Action) in v1.3. |
| **Chat-style narration** | Avatar + bubble narration system replaced by cleaner topic + sentence flow structure. The "chat" metaphor gave way to a more direct narrative sequence. |

### Key decisions & rationale
| Decision | Rationale |
|----------|-----------|
| **JS `.reveal` class over CSS phase system** | Old CSS phase system required specific scroll thresholds and suffered from specificity battles. New system toggles `.reveal` class every frame with gated thresholds. |
| **30-70 split layouts** | 30% of viewport for conversation narration, 70% for interactive UI. Replaces single-column centered layout. |
| **Chat-style narration** | Avatar ("U") + "Univens" label + subtle bubble background creates intimate 1:1 conversation feel. |
| **No card containers** | Chapter 18 compliance: whitespace replaces borders. Cards use padding and subtle `rgba(17,17,17,.03)` backgrounds instead of shadows. |
| **Single HTML file** | All CSS (inline `<style>`) and JS (inline `<script>`) remain embedded. No build step. |
| **Inter Tight + Instrument Serif + IBM Plex Mono** | Replaced single Inter font with three-font system: Inter Tight for body/UI, Instrument Serif for `<em>` emphasis, IBM Plex Mono for labels/meta. |
| **Contrast-based color system** | Replaced arbitrary opacities with controlled semantic tokens: `--text-primary` (100%), `--text-secondary` (82%), `--text-tertiary` (62%), `--text-muted` (42%), `--text-disabled` (24%). No pure grays, no `#000`/`#fff` extremes. |

### Copywriting approach
- Written from Univens' perspective (first-person "we")
- Directly addresses the visitor as "you" — 1:1 conversation
- No marketing buzzwords (no "innovation", "synergy", "digital transformation")
- **Micro-chat style**: Each section leads with a single conversational sentence in a chat bubble. Subtext (supporting copy) only used when the heading alone cannot deliver the message.
- The tone is confident, honest, and slightly informal — like a real CEO conversation
- 9 sections with a natural narrative arc: problem → understanding → solution → close

---

## 2. Technical Architecture

### 2.1 Layout approach: Fixed-stage + spacer
This is the core architectural decision and must NOT be changed:

```
┌─────────────────────────────┐
│  .stage (position: fixed)   │  ← All content lives here, locked in viewport
│  ┌───────────────────────┐  │
│  │  .section-block × 9   │  │  ← Absolutely positioned, stacked, opacity-controlled
│  │  ┌─────────────────┐  │  │
│  │  │  .nf-flow       │  │  │  ← Vertical narrative: Topic → Sentence → Visual → Support → Action
│  │  │  .nf-topic      │  │  │  ← Section label (mono, uppercase)
│  │  │  .nf-arrow      │  │  │  ← ↓ connector between each step
│  │  │  .nf-sentence   │  │  │  ← Main message
│  │  │  .nf-visual     │  │  │  ← Primary UI component
│  │  │  .nf-support    │  │  │  ← Explainer text
│  │  │  .nf-action     │  │  │  ← CTA buttons
│  │  └─────────────────┘  │  │
│  └───────────────────────┘  │
├─────────────────────────────┤
│  #spacer (height: 1000vh)   │  ← Creates scroll distance, pushes footer down
├─────────────────────────────┤
│  footer                     │
└─────────────────────────────┘
```

**Why this works:** The `.stage` is `position: fixed` so its content never moves. The `#spacer` div (inline `height: 1000vh` = 9 slides × 100vh snap budget) creates real scroll distance. JavaScript maps `window.scrollY` through the spacer to compute which section is active.

**Critical CSS:**
```css
.stage{position:fixed;top:0;left:0;width:100%;height:100vh;overflow:hidden;z-index:2}
.section-block{position:absolute;inset:0;top:clamp(80px,10vh,110px);display:flex;align-items:flex-start;padding:clamp(48px,5vw,80px) clamp(32px,6vw,96px);opacity:0;pointer-events:none;transition:opacity .7s cubic-bezier(.22,1,.36,1)}
.section-block.active{pointer-events:auto}
#spacer{pointer-events:none}
```

### 2.2 Scroll math

```javascript
function updateSlides(){
  const maxScroll = spacer.offsetHeight - window.innerHeight;
  const progress = Math.min(1, window.scrollY / maxScroll);
  const rawIndex = progress * (TOTAL - 1); // TOTAL = 9

  sections.forEach((sec, i) => {
    const diff = rawIndex - i;
    const o = opacity based on diff;
    sec.style.opacity = o;

    // Phase reveal (JS-driven, not CSS)
    const lp = Math.min(1, Math.max(0, diff / .55 + .5)); // local progress 0-1
    const isVisible = o > 0.01;
    sec.querySelectorAll('.phase-a').forEach(el => {
      el.classList.toggle('reveal', isVisible && lp > 0.02);
    });
    sec.querySelectorAll('.phase-b').forEach(el => {
      el.classList.toggle('reveal', isVisible && lp > 0.1);
    });
  });
}
```

- **Fade in:** ~18% of scroll budget
- **Full visibility:** ~55% of scroll budget
- **Fade out:** ~13% of scroll budget
- **Phase thresholds:** `lp > 0.02` for `.phase-a`, `lp > 0.1` for `.phase-b`
- **Snap scroll:** Wheel/key snaps to nearest section with 550ms easeInOutQuad; fast scroll >0.5 px/ms collapses phase thresholds

### 2.3 Preloader

Custom preloader after nikolaradeski.com:
- Fullscreen `var(--bg)` overlay, `z-index: 999999`
- "Univens" reveals letter by letter (0.07s delay, riseUpFade animation)
- After 1.1s, cycles through 5 phrases at 700ms intervals:
  - "The conversation starts here."
  - "One decision at a time."
  - "Built on execution."
  - "Strategy meets reality."
  - "Let's build."
- Fades out 500ms after last phrase
- Also hides immediately on tab switch (`visibilitychange`) or page unload (`pagehide`)

---

## 3. Design System

### 3.1 Colors
| Token | Value | Usage |
|-------|-------|-------|
| `--bg` | `#fff` | Page, stage, nav, footer, preloader |
| `--text-primary` | `#111` | Headings, narration text, strong emphasis (100% opacity equivalent) |
| `--text-secondary` | `rgba(17,17,17,.82)` | Body copy, explanations (82%) |
| `--text-tertiary` | `rgba(17,17,17,.62)` | Supporting text, footnotes (62%) |
| `--text-muted` | `rgba(17,17,17,.42)` | Labels, secondary CTA, timestamps (42%) |
| `--text-disabled` | `rgba(17,17,17,.24)` | Minimum contrast elements (24%) |
| `--border` | `rgba(17,17,17,.08)` | Dividers, card borders, subtle lines |
| `--border-s` | `rgba(17,17,17,.14)` | Stronger borders, button outlines |
| `--selection` | `rgba(17,17,17,.08)` | Text selection highlight (dark tint) |

No pure grays. No random opacities. Every value derives from `#111` with controlled alpha.

### 3.2 Typography

```
font-family: 'Inter Tight', sans-serif (weights 400, 450, 500, 600, 700)
font-family: 'Instrument Serif', serif (weight 400, <em> only)
font-family: 'IBM Plex Mono', monospace (weights 400, 500)
```

| Class | Size | Weight | Usage |
|-------|------|--------|-------|
| `.narration-text` | `clamp(13px,.85vw,17px)` | 500 | Chat bubble text |
| `.invite-large` | `clamp(16px,1.2vw,24px)` | 500 | CTA section heading |
| `.invite-support` | `clamp(11px,.7vw,14px)` | 400 | CTA support text |
| `.btn` | `12px` | 500 | Primary button |
| `.btn-ot` | `11px` | 400 | Secondary CTA |
| `.phi-text` | `clamp(11px,.7vw,14px)` | 400 | Philosophy card text |
| `.metric-val` | `clamp(18px,2.5vw,32px)` | 600 | Metric number |
| `.metric-lbl` | `10px` | 500 | Metric label (mono) |
| `.case-title` | `clamp(14px,1vw,18px)` | 600 | Case study title |
| `.case-desc` | `clamp(11px,.7vw,14px)` | 400 | Case study description |
| `.principle-item` | `clamp(13px,.8vw,16px)` | 500 | Principle text |
| `.ts-label` | `clamp(13px,.8vw,16px)` | 500 | Timeline step label |
| `.conv-snip` | `clamp(10px,.6vw,13px)` | 400 | Conversation snippet |
| `.mlogo` | `clamp(10px,.6vw,13px)` | 500 | Logo wall item (mono) |
| `.status-item` | `clamp(11px,.7vw,14px)` | 500 | Hero status cycling |
| `nav .logo` | `clamp(13px,.9vw,15px)` | 500 | Nav logo |
| `nav .cta` | `clamp(13px,.8vw,15px)` | 500 | Nav CTA |

**Typography rules:**
- Font weights 100–300 never used
- Weight 400: body copy, explanations
- Weight 450: preferred body weight
- Weight 500: UI labels, nav, buttons, principles
- Weight 600: section titles, metric values, case titles
- Weight 700: hero only (if used). Never exceed 700.
- Never italicize Inter Tight — `<em>` uses Instrument Serif with `font-style:normal`
- `text-wrap:balance` on headings
- Uppercase allowed only on: navigation, labels, IBM Plex Mono elements, tiny metadata
- Uppercase forbidden in: paragraphs, buttons, headlines

### 3.3 Spacing
- **Split gap:** `clamp(16px,2vw,32px)` between left (narration) and right (UI)
- **Section padding:** `clamp(48px,5vw,80px)` top, `clamp(32px,6vw,96px)` left/right
- **Narration bubble padding:** `clamp(8px,.5vw,10px)` vertical, `clamp(10px,.6vw,12px)` horizontal
- **Grid gaps:** Philosophy grid `clamp(12px,1.2vw,20px)`, Case grid `clamp(16px,2vw,24px)`, Workspace grid `clamp(12px,1.2vw,20px)`
- **Inner max-width:** `clamp(320px,38vw,540px)` for sections without inline `max-width:none`

### 3.4 Layout variants per section

| Slide | Layout | Topic | Sentence | Visual | Support | Action |
|-------|--------|-------|----------|--------|---------|--------|
| 00 (Hero) | Left-aligned | Introduction | Good to have you here. | Status column | Value prop | CTA + link |
| 01 (Expectations) | Left-aligned | Expectations | Here's what you can expect. | Principles + Philosophy | Note | Link |
| 02 (Credibility) | Left-aligned | Credibility | Different businesses. Different challenges. | Logos + Metrics | Footnote | Link |
| 03 (Capabilities) | Left-aligned | Capabilities | Challenges rarely exist in isolation. | Capability map | Objective note | Link |
| 04 (Work) | Left-aligned | Selected Work | Execution is where strategy meets reality. | Case grid | Simplification note | Link |
| 05 (Process) | Left-aligned | How We Work | Clear journeys. Shared accountability. | Process map | Execution note | Link |
| 06 (Experience) | Left-aligned | Client Experience | Proof lives in conversations. | Conversation stack | Relationship note | Link |
| 07 (About) | Left-aligned | About | Built on a philosophy of building. | Workspace grid | Philosophy note | Link |
| 08 (Start) | Centered | Start | Enough about us. Let's talk about your business. | Input field | Support line | CTA + link |

### 3.5 Buttons

**Primary (`.btn`):**
- Outlined: `1px solid var(--text-primary)`, transparent background
- On hover: fills black, text stays dark
- Padding: `clamp(8px,.6vw,12px) clamp(16px,1.5vw,20px)`, font: `12px / 500`
- Border-radius: `4px`
- Smooth `0.4s` spring easing transition

**Secondary (`.btn-ot`):**
- Text only: no border, no background
- Low opacity text: `var(--text-tertiary)`
- On hover: text darkens
- Padding: `8px 2px`, font: `11px / 400`

---

## 4. Component Inventory

### 4.1 Fixed elements (z-index order)

| Component | z-index | Description |
|-----------|---------|-------------|
| Preloader | 999999 | Fullscreen light overlay, hides after letter animation |
| Noise overlay | 0 | SVG film grain, `opacity:.02` |
| Stage (text) | 2 | Fixed container for all section content |
| Context memory | 99 | Previous section's key sentence for conversational continuity |
| Nav | 100 | Logo (left) + CTA + hamburger, always visible |
| Menu overlay | 200 | Fullscreen nav menu with 9 section links |
| Footer | 3 | Contact info, `position:relative;background:var(--bg)` |
| Cursor | 1080 | Dark dot + gap-ring, state machine, hidden on mobile |

### 4.2 Content sections (9 viewports)

| # | Section | Flow (Topic → Sentence → Visual → Support → Action) |
|---|---------|------------------------------------------------------|
| 0 | Hero | Introduction → "Good to have you here." → Status column → Value prop → CTAs |
| 1 | Expectations | Expectations → "Here's what you can expect." → Principles + Philosophy cards → Note → Link |
| 2 | Credibility | Credibility → "Different businesses. Different challenges." → Logos + Metric wall → Footnote → Link |
| 3 | Capabilities | Capabilities → "Challenges rarely exist in isolation." → Capability map → Objective note → Link |
| 4 | Selected Work | Selected Work → "Execution is where strategy meets reality." → Case grid → Note → Link |
| 5 | How We Work | How We Work → "Clear journeys. Shared accountability." → Process map → Note → Link |
| 6 | Client Experience | Client Experience → "Proof lives in conversations." → Conversation stack → Note → Link |
| 7 | About | About → "Built on a philosophy of building." → Workspace grid → Note → Link |
| 8 | Start | Start → "Enough about us. Let's talk about your business." → Input → Support → CTAs |

### 4.3 Narrative flow system

Each section uses a clean vertical flow: **Message → Visual → Action**, with no connectors, labels, or support text.

```html
<div class="nf-flow">
  <div class="nf-line phase-a">Core message line 1.</div>
  <div class="nf-line phase-a">Core message line 2.</div>
  <div class="nf-visual phase-b">
    <!-- Primary UI component -->
  </div>
  <div class="nf-action phase-b">
    <a href="#" class="btn">Action →</a>
    <a href="#" class="btn-ot">Secondary →</a>
  </div>
</div>
```

- `.nf-flow`: flex column, `max-width: clamp(480px,52vw,720px)`, left-aligned by default
- `.nf-flow.nf-centered`: centered alignment for closing section (V09)
- `.nf-line`: single message line, `clamp(17px,1.3vw,26px)` / weight 500. Multiple `.nf-line` elements stack with `4px` gap
- `.nf-visual`: full-width wrapper for the primary UI component
- `.nf-action`: flex row (or column for centered) wrapping CTAs with 12px gap
- Phase-a reveals the message lines; phase-b reveals visual + action
- No arrows, no topic labels, no footnotes — just the essential message

### 4.4 Navbar

```
[star] Univens                                Let's talk → [☰]
```

- Always visible, no hide/show on scroll
- `pointer-events:none` on nav, `pointer-events:auto` on children
- Hamburger menu opens overlay with 9 section links + CTA
- `.menu-overlay` with backdrop blur, `z-index:200`

### 4.5 Preloader

- Fullscreen `var(--bg)` overlay with `z-index: 999999`
- "Univens" animates letter by letter (riseUpFade)
- Cycles through 5 brand phrases at 700ms intervals
- `transition: opacity .5s ease` for fade out
- Auto-hides on `visibilitychange` and `pagehide`

---

## 5. Animation System

### 5.1 Phase reveals (JS-driven `.reveal` class)

Replaced the old CSS `data-phase` system with direct JS toggling:

- `.phase-a` / `.phase-b` base: `opacity:0; transform:translateY(12px); transition:opacity .55s, transform .55s`
- `.phase-a.reveal` / `.phase-b.reveal`: `opacity:1!important; transform:translateY(0)!important`
- Thresholds: `lp > 0.02` for `.phase-a`, `lp > 0.1` for `.phase-b`
- Gated by `isVisible = o > 0.01` flag to prevent premature removal
- `!important` guarantees override against competing opacity rules

### 5.2 Word-by-word blur-in (`.ch` elements)
- Each `.ch` span starts with `opacity:0; filter:blur(10px)`
- When parent `.section-block` gets `.active` class, transitions to `opacity:1; filter:blur(0)`
- Duration: `1.2s` with spring easing
- `.active` is toggled bidirectionally (add on enter, remove on exit)

### 5.3 Preloader animation
- `@keyframes riseUpFade`: `opacity:0; transform:translateY(24px)` → `opacity:1; transform:translateY(0)`
- `0.25s` duration with `cubic-bezier(.22,1,.36,1)` spring easing
- Letter-by-letter delay: `0.07s` per character
- Cycling phrases: plain text swap via `textContent` (no animation)

### 5.4 Cursor state machine
Full cursor system with:
- Spring physics tracking (`SK=0.12, SD=0.68`)
- Magnetic attraction to interactive elements
- Curiosity stretch along velocity vector
- Gap-ring completion (scroll-responsive + hover-completes)
- Resting blink after 20s inactivity
- Section transition pulse
- Scroll velocity fade
- Contextual whisper labels
- Element-type-aware ring response
- Scroll particle trail

---

## 6. Background layers (rendering order, bottom to top)

1. **Noise** (`#noise`, `z-index:0`) — SVG film grain at 2% opacity
2. **Stage** (`z-index:2`) — all section content, fixed position
3. **Context memory** (`z-index:99`) — previous section sentence, top-left
4. **Nav** (`z-index:100`) — logo + CTA + hamburger
5. **Menu overlay** (`z-index:200`) — fullscreen nav menu
6. **Cursor** (`z-index:1080`) — custom pointer with state machine
7. **Preloader** (`z-index:999999`) — on top of everything, removed after load

---

## 7. Constraints (must NOT be changed)

- **No position:fixed alternative** — Must use `.stage` (fixed) + `#spacer` pattern
- **No blur/translateY/transform on scroll transitions** — Section-level opacity only
- **No text movement on scroll** — Only opacity fades between sections
- **Sections must not visibly overlap** — Only one section visible at any scroll position
- **Fixed-stage architecture is required** — Do not switch to natural scroll or sticky-based approaches
- **Single HTML file** — All CSS and JS must remain embedded
- **Font stack** — Inter Tight (body/UI), Instrument Serif (`<em>` only), IBM Plex Mono (labels)
- **No font weight below 400 or above 700**
- **No italic Inter Tight** — `<em>` must use Instrument Serif with `font-style:normal`
- **Color system** — All colors derive from `#111` with controlled alpha; no pure grays, no `#000`/`#fff`
- **Chapter 18 Visual Language System** — No glassmorphism, no decorative canvases, no card containers (whitespace replaces borders), no shadows, max one icon per info group, noise max 2%, approved corner radii (4px cards, 2px small elements)
- **Narrative flow layout** — Each section uses the 5-element vertical flow: Topic → ↓ → Sentence → ↓ → Visual → ↓ → Support → ↓ → Action
- **Custom cursor** — Must be present on desktop (≥1024px), hidden on mobile (≤1023px)
- **Phase system** — Phase-a at `lp > 0.02`, Phase-b at `lp > 0.1`, gated by `isVisible`
- **White background** — `var(--bg): #fff`
- **Selection color** — `var(--selection): rgba(17,17,17,.08)`
- **Preloader** — Must remain light mode (`var(--bg)` background), letter-rise for "Univens" + cycling phrases

---

## 8. Preferences (can be adjusted with care)

- Preloader phrase content and timing
- Phase threshold values (`lp > 0.02` / `lp > 0.1`)
- Narration text and copy
- Button style as long as it feels conversational
- Typography sizes if the premium feel is maintained
- Grid column counts and breakpoints
- Cursor behavior parameters
- Snap scroll duration and easing
- Footer content and structure

---

## 9. File structure

```
univens/
├── index.html    # Single-file page (~889 lines)
│   ├── <head>    # Fonts, inline styles (~230 lines)
│   ├── <body>    # Preloader, cursor, noise, nav, menu, stage, spacer, footer, script
│   └── <script>  # Scroll engine, reveal system, cursor state machine, interactives, preloader
├── theme.md      # This document
└── logo.png      # (exists but unused; using text logo + star icon instead)
```

---

## 10. Common pitfalls for AI agents

1. **Don't remove the fixed-stage pattern** — It's the core UX. Natural scroll breaks the "text stays fixed" constraint.
2. **The `.reveal` class is toggled every frame** — It's not additive like the old `active` class. `updateSlides()` runs on every rAF tick and sets/removes `.reveal` based on current scroll position.
3. **Phase thresholds are gated by `isVisible`** — Without `isVisible = o > 0.01`, `.reveal` would be removed too early as section opacity crosses zero.
4. **`!important` on `.reveal` is intentional** — It guarantees override against competing opacity rules from transition base states.
5. **`.section-block` selector prefix on phase rules** — Rules use `.section-block .phase-a` not `.inner .phase-a` to catch elements outside `.inner`.
6. **Preloader must hide on `visibilitychange`** — Without this, the preloader could persist when returning to the tab via browser cache.
7. **The narrative flow is 5 elements, not 3** — Each section must have exactly: Topic, Arrow, Sentence, Arrow, Visual, Arrow, Support, Arrow, Action. Miss one and the structure breaks.
8. **Tag mismatches break all JS** — A single unclosed `<div>` or `<span>` can halt the entire script. Verify tag balance after edits.
9. **`.nf-flow.nf-centered` is for V09 only** — The centered modifier aligns everything center. All other sections use the default left-aligned flow.
10. **Old `level-01` through `level-04`, narration, and split classes are removed** — Do not reference them. Use `.nf-*` classes for the flow.

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

Append entries here at the end of each work session.

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
- Stripped nav to ultra-minimal: logo (left) + "Book a Call" (right), always visible
- Added vertical progress indicator on right edge
- Removed fixed-stage → switched to natural scroll (user later rejected)

### Session 3 — Restored fixed-stage architecture
- Reverted natural scroll → restored fixed-stage + spacer as the core UX
- Kept visual improvements: narrow editorial column (36em), progress dot, minimal nav, improved typography
- Fixed spacer height: moved from JS-set to inline `style="height:600vh"`
- Upgraded typography: larger body text, darker colors, bolder ledes

### Session 4 — Text transitions & button redesign
- Added word-by-word blur reveal on lede text
- Added paragraph stagger animation
- Redesigned buttons: primary (outlined → fills black), secondary (text-only with arrow reveal)
- Created this `theme.md` document

### Session 5 — Bidirectional text transitions
- Changed `.active` from additive to toggled for scroll-back support
- Switched paragraph stagger from CSS animation to CSS transition for bidirectional replay
- Removed `@keyframes fpi`

### Session 6 — Heading-first content reveal
- Shifted body paragraph delays by +2s so lede word-reveal completes before body content

### Session 7 — Enhanced progress indicator
- Replaced subtle progress bar with 2px × `min(55vh,360px)` bar
- Added 10 section markers + section number label

### Session 8 — Cursor redesign (gap-ring system)
- Completely redesigned the cursor around the concept of "completion"
- Gap-ring rendered via `conic-gradient` + `mask` for dynamic arc
- Scroll-responsive gap, hover completion, idle breath, click snap
- Default cursor hidden on desktop, hidden entirely on mobile
- Removed `mix-blend-mode: difference` — uses explicit dark colors

### Session 9 — Value-driven cursor features
- Contextual whisper labels for interactive elements
- Element-type-aware ring response (primary, secondary, links, cards)
- Section-type resonance (problem, solution, closing)
- Scroll velocity fade

### Session 10 — Cursor state machine
- Full cursor JS restructured as a state machine
- Magnetic attraction (gravity well within 60px)
- Curiosity stretch along velocity vector
- Continuous organic breathing (~4.8s cycle)
- Resting blink after 20s inactivity
- Section transition pulse
- Scroll particle trail
- Text reading mode (ring gap opens over `<p>` elements)

### Session 11 — Full blueprint implementation & cinematic experience system
- **Complete rewrite**: Replaced all 10 sections with 11 viewports matching Experience Blueprint
- **Architecture scaled**: spacer from 600vh→660vh, progress indicator to 11 markers
- **New interactions**: Hero word-fade, V2 choices, V3 nodes canvas, V4 logo wall, V5 gravity, V6 timeline, V7 conv snippets, V8 wrong hire flow, V9 poster typography, V10 workspace scene, V11 input field
- **Cursor upgrades**: Spring physics replaces lerp, arrow/pencil morph
- **Phase system**: `.phase-a`/`.phase-b` layers controlled by `data-phase` attribute
- **Snap scroll**: Replaced continuous scroll with section-by-section snapping
- **Spacer resized** to 1100vh (11 × 100vh)
- **Modern typographic refinements**: CSS custom properties, warm off-white `#faf8f5` background
- **Chapter 15 Typography Motion Language**: Sentence-by-sentence reveal, Soft Fade Up, Context Memory
- **Conversation V3 rewrite**: Full 11-viewport content replacement
- **Chapter 02 Design Concept**: Removed borders/containers, whitespace replaces containers

### Session 12 — v1.2 UI-first architecture (major restructure)
- **9 viewports**: Reduced from 11 (V6.0) to 9 (v1.2). Sections: Hero, Expectations, Credibility, Capabilities, Work, Process, Experience, About, Start.
- **30-70 split layouts**: All sections restructured to 30% narration (left) + 70% UI (right). Hero kept at 70-30.
- **Micro-chat copy**: Complete rewrite. Each heading is a single conversational line. Subtext only when heading alone cannot deliver the message.
- **New UI components**: Philosophy Cards, Metric Wall, Case Study Grid, Workspace Grid, Conversation Snippets, Capability Map, Process Map.
- **JS-driven `.reveal` class**: Replaced CSS `data-phase` system. `updateSlides()` toggles `.reveal` class every frame with gated thresholds.
- **CSS cleanup**: Removed all unused selectors (`.stmts`, `.stmt`, `.outcome-wrap`, `.outcome`, `.eco-viz`, `.net-viz`, etc.). Removed old `level-01` through `level-04` classes. Removed `.inner p` base rule.
- **Border radius**: Standardized to 4px for cards, 2px for small elements per Chapter 18.
- **Chapter 18 compliance**: No glassmorphism, no decorative canvas, no card containers, no shadows, noise ≤2%.
- **Color system**: Replaced arbitrary opacities with controlled tokens (`--text-primary:#111`, etc.).
- **Font stack**: Inter Tight + Instrument Serif + IBM Plex Mono.

### Session 13 — Micro-chat narration system
- **Chat-style left column**: Replaced static headings with `.narration` system: avatar ("U" in circle), "UNIVENS" label, subtle bubble background, asymmetric border-radius.
- **Narration CSS**: `.narration`, `.narration-msg`, `.narration-sender`, `.narration-avatar`, `.narration-name`, `.narration-text`.
- **Context memory synced**: 9 entries matching new section headings.
- Removed `.inner p` base rule and `.inner p.sub` (unused or conflicting with footnote specificity).

### Session 14 — Custom preloader
- **Preloader added**: Inspired by nikolaradeski.com.
- Light mode: `var(--bg)` background, `var(--text-primary)` text.
- "Univens" letter-by-letter reveal via `riseUpFade` animation.
- Cycling phrases: "The conversation starts here." → "One decision at a time." → "Built on execution." → "Strategy meets reality." → "Let's build."
- Initially dark mode (`#111` bg, `#fff` text), changed to light mode per user request.
- 700ms interval per phrase, 500ms fade out.
- Auto-hide on `visibilitychange`/`pagehide`.

### Session 18 — Clean narrative flow: no arrows, no topics, minimal copy
- **Removed all arrows (↓)**: Every `.nf-arrow` element stripped from all 9 sections. The sequential flow is implied by vertical stacking alone.
- **Removed all topic labels**: `.nf-topic` (Introduction, Expectations, Credibility, etc.) removed. Sections speak for themselves.
- **Removed all support text**: `.nf-support` removed. No footnotes, no explainers — just the essential message.
- **Copy simplified to 1–2 punchy lines per section**: Hero → "Good to have you here. / Let's start with what we do." | Expectations → "What brings businesses to Univens? / Execution." | Credibility → "Different businesses. / Different challenges." | Others trimmed to single lines.
- **New `.nf-line` class**: Single class for all message lines (replaces `.nf-topic`, `.nf-sentence`, `.nf-support`). Stacked via `.nf-line + .nf-line` margin.
- **CSS cleanup**: Removed `.nf-topic`, `.nf-arrow`, `.nf-sentence`, `.nf-support`, `.scroll-hint`, `.status-arrow`, `@keyframes hintIn`. Cursor scroll-hint logic removed. File reduced 956 → 889 lines.
- **Context memory refreshed**: Messages updated to match new copy. No section prefix — just the raw line.

### Session 17 — Narrative flow restructure (v1.3)
- **Complete viewport redesign**: Every section restructured to `Topic → ↓ → Sentence → ↓ → Visual → ↓ → Support → ↓ → Action` vertical narrative flow. Replaces the 30-70 split + narration bubble system.
- **New CSS**: `.nf-flow` (flex column container), `.nf-topic` (mono section label), `.nf-arrow` (↓ connector), `.nf-sentence` (main message), `.nf-visual` (component wrapper), `.nf-support` (explainer text), `.nf-action` (CTA wrapper). Added `.nf-centered` modifier for centered alignment.
- **All 9 sections rewritten**: Hero (Introduction), Expectations, Credibility, Capabilities, Selected Work, How We Work, Client Experience, About, Start (centered) — each using the 5-element flow structure with phase-a (topic+sentence) and phase-b (visual+support+action) reveal.
- **CSS cleanup**: Removed `.narration*`, `.split-layout*`, `.level-05`, `.principles-note`, `.capability-footnote`, `.process-footnote`, `.conv-footnote`, `.mlogos-footnote`, `.ws-footnote`, `.timeline*`, `.experience-stack`, `.invite-large`, `.invite-support`, `.status-hint`, `.timeline-footnote` classes. Removed `.vp-hero .inner{max-width:none}`. Updated `.inner` default to `min(92vw,800px)`.
- **JS updates**: Context memory messages updated to `"Topic — Sentence"` format. `ST` array updated (last entry `'invitation'`). Menu label "Hero" → "Introduction".
- **File size reduced**: ~1008 → ~956 lines.

### Session 16 — Workspace URL fix & conv-stack ID
- Fixed truncated Unsplash photo IDs in V08 workspace grid (`index.html:526-529`): photo IDs were cut off at `//auto=format`, replaced with valid Unsplash IDs for Planning, Design, Code, and Team images.
- Added `id="conv-stack"` to V07 conversation stack container (`index.html:502`) so JS finds it via `getElementById` instead of falling back to `querySelector('.conv-stack')`.

### Session 15 — theme.md rewrite
- Full document rewrite to reflect v1.2 architecture, design system, and constraints.
- Updated all sections: Project Identity, Architecture, Design System, Components, Animation, Constraints, Director's Briefs.
- Preserved all historical session entries (sessions 1-11), appended sessions 12-14.
- Removed Cinematic Experience System section (no longer maintained for current v1.2).
- Updated Director's Briefs to match 9 viewports.

---

## 13. Director's Briefs

### V00 — Hero
| Field | Value |
|---|---|
| **Narrative Purpose** | Welcome the visitor. Set the conversational tone. |
| **Primary Emotion** | Curiosity, calm |
| **Message** | "Good to have you here." + "Let's start with what we do." |
| **Visual** | Status cycling column |
| **Action** | Start the Conversation → / See How We Think |
| **Phase-a** | Message |
| **Phase-b** | Visual + Action |

### V01 — Expectations
| Field | Value |
|---|---|
| **Narrative Purpose** | Set expectations for the relationship. |
| **Primary Emotion** | Recognition |
| **Message** | "What brings businesses to Univens?" + "Execution." |
| **Visual** | Principles + Philosophy cards |
| **Action** | Learn More → |
| **Phase-a** | Message |
| **Phase-b** | Visual + Action |

### V02 — Credibility
| Field | Value |
|---|---|
| **Narrative Purpose** | Build trust through evidence, not claims. |
| **Primary Emotion** | Trust |
| **Message** | "Different businesses." + "Different challenges." |
| **Visual** | Logo wall + Metric wall |
| **Action** | See Our Work → |
| **Phase-a** | Message |
| **Phase-b** | Visual + Action |

### V03 — Capabilities
| Field | Value |
|---|---|
| **Narrative Purpose** | Show breadth of capability without a service menu. |
| **Primary Emotion** | Understanding |
| **Message** | "Challenges rarely exist in isolation." |
| **Visual** | Capability map |
| **Action** | Explore Services → |
| **Phase-a** | Message |
| **Phase-b** | Visual + Action |

### V04 — Selected Work
| Field | Value |
|---|---|
| **Narrative Purpose** | Prove execution through real examples. |
| **Primary Emotion** | Confidence |
| **Message** | "Execution is where strategy meets reality." |
| **Visual** | Case study grid |
| **Action** | View All Work → |
| **Phase-a** | Message |
| **Phase-b** | Visual + Action |

### V05 — How We Work
| Field | Value |
|---|---|
| **Narrative Purpose** | Explain the process transparently. |
| **Primary Emotion** | Clarity |
| **Message** | "Clear journeys." + "Shared accountability." |
| **Visual** | Process map |
| **Action** | Start Your Project → |
| **Phase-a** | Message |
| **Phase-b** | Visual + Action |

### V06 — Client Experience
| Field | Value |
|---|---|
| **Narrative Purpose** | Replace testimonials with authentic evidence. |
| **Primary Emotion** | Connection |
| **Message** | "Proof lives in conversations." |
| **Visual** | Conversation snippets |
| **Action** | Let's Talk → |
| **Phase-a** | Message |
| **Phase-b** | Visual + Action |

### V07 — About
| Field | Value |
|---|---|
| **Narrative Purpose** | Humanize Univens — show the philosophy. |
| **Primary Emotion** | Warmth |
| **Message** | "Built on a philosophy of building." |
| **Visual** | Workspace grid |
| **Action** | Meet Us → |
| **Phase-a** | Message |
| **Phase-b** | Visual + Action |

### V08 — Start
| Field | Value |
|---|---|
| **Narrative Purpose** | Start the relationship, not capture a lead. |
| **Primary Emotion** | Readiness |
| **Message** | "Enough about us." + "Let's talk about your business." |
| **Visual** | Input field |
| **Action** | Start the Conversation → / Explore Our Work |
| **Phase-a** | Message |
| **Phase-b** | Visual + Action |
