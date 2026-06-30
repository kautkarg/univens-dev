# Univens — Experience System Design

> **How to use this doc:** Read it cover-to-cover before making any changes. It defines the architecture, design language, and decision history of the Univens Experience System. This is NOT a homepage spec — it is the system from which all Univens pages (Home, About, Services, Work, Insights, Contact) will eventually be composed. Every principle here should be evaluated against: *"Would every page inherit this behavior?"* If yes, it's a system decision. If no, it's a page decision and does not belong here.

---

## 1. Project Identity

**Univens** is an execution partner for growing businesses. The Univens Experience System defines how the brand communicates, behaves, and feels — independent of any specific page. This document codifies the visual, motion, interaction, typography, color, layout, host, cursor, scroll, and information philosophy that every Univens page will inherit.

### Core principles
- **Conversational** — The visitor is being spoken to directly, not marketed at. This is a system-level tone decision, not a homepage decision.
- **System-first** — Every behavior must be evaluable against: *"Should every page inherit this?"* If no, it belongs in page-level composition, not the system.
- **Premium minimal** — Warm off-white background (`#F8F9FA`), dark gray text (`#1A1C1E`), generous whitespace, dramatic typography contrast (Geist headings vs Inter Tight body)
- **Scroll-driven narrative** — Stage stays fixed; opacity (smoothstep easing) and `.reveal` class handle transitions
- **Cinematic presentation** — Letterbox bars, multi-stop vignette overlay, film grain noise, spring-physics cursor with morphing states, host intelligence FSM in bottom-right
- **Curiosity system** — The experience should invite exploration, not demand attention. Phase-based reveals, host intelligence, and cursor engagement arc reward the visitor for spending time.

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
| **Nav with step counter + progress bar** | Previous navbar had step counters and progress bars. Stripped to just logo + hamburger. |
| **Marketing copy** | Original copy was standard agency language. Completely rewritten in CEO conversational voice. |
| **CSS phase system** | Old system used `.level-01`–`.level-05` CSS classes with opacity transitions controlled by `data-phase` attributes. Replaced with JS-driven `.reveal` class for reliable visibility control. |
| **Canvas blobs** | Background canvas with warm gradient blobs was removed (no decorative elements). |
| **30-70 split layout** | Split layouts (narration left, UI right) replaced by `.inner`-based centered/asymmetric layout in current version. |
| **Chat-style narration** | Avatar + bubble narration system replaced by direct headline/body structure with context memory. |
| **Narrative flow (`.nf-*`) classes** | Old `.nf-flow`, `.nf-line`, `.nf-visual`, `.nf-action` system was replaced by flexible `.inner` + `.inner-right`/`.inner-left` structure in Session 19. |
| **IBM Plex Mono** | Replaced with Fragment Mono (smaller x-height, more character for labels and section numbers). |

### Key decisions & rationale
| Decision | Rationale |
|----------|-----------|
| **JS `.reveal` class over CSS phase system** | CSS phase system required specific scroll thresholds and suffered from specificity battles. New system toggles `.reveal` class every frame with gated thresholds. |
| **Premium visual polish (Session 19)** | User requested Apple-level whitespace, dramatic typography contrast, and cinematic depth. Added: expanded padding, bigger Geist headlines + softer Inter Tight body, enhanced backdrop-blur, vignette, box-shadows on hover. |
| **Asymmetric layout (deprecated Session 22)** | Originally introduced in Session 19 (`.inner-right`/`.inner-left`). Removed in Session 22 — all sections now use centered `.inner`. Asymmetry was a page-level decision, not a system-level one. |
| **Geist + Inter Tight + Fragment Mono** | Geist for headings (modern geometric presence), Inter Tight for body (readable warmth), Fragment Mono for labels (more character than IBM Plex Mono). |
| **Cinematic presentation** | Letterbox bars (gradient), multi-stop vignette, film grain noise (2%, multiply), spring-physics cursor with morphing — create a premium film-like atmosphere without being decorative. |
| **Host FSM system** | Bottom-right contextual messenger with 8 states (ARRIVED→WELCOME→HOVERING→EXPLORING→RETURNING→DEEP_DIVE→READY_TO_LEAVE). Replaces traditional tooltips/onboarding with an intelligent guide. |
| **Contrast-based color system** | Semantic tokens: `--text-primary` (100%), `--text-secondary` (74%), `--text-tertiary` (48%), `--text-muted` (28%), `--text-disabled` (13%). Colors derive from `#1A1C1E` (dark gray), not `#000`. Background is warm off-white `#F8F9FA`. |
| **Single HTML file** | All CSS (inline `<style>`) and JS (inline `<script>`) remain embedded. No build step. |

### Copywriting approach
- Written from Univens' perspective (first-person "we")
- Directly addresses the visitor as "you" — 1:1 conversation
- No marketing buzzwords (no "innovation", "synergy", "digital transformation")
- Headlines are bold, declarative statements. Body subheads explain briefly. Interactive UI components carry the detailed storytelling.
- The tone is confident, direct, and premium — like a CEO in a boardroom conversation
- 8 sections with a natural narrative arc: what → how → proof → can they → show me → what's it like → who → let's talk

---

## 1b. Phase Architecture

The Univens project follows a phased approach. Each phase has a distinct objective and output. Earlier phases define the inputs for later phases.

### Phase 01 — Brand Core (Complete)
**Output:** Personality, Positioning, Communication, Mental Model
- Who Univens is and how it speaks
- The conversational 1:1 CEO voice established
- Completed before system design began

### Phase 02 — Experience System (Current)
**Output:** Visual language, motion language, interaction language, typography language, color language, layout language, host behavior, cursor behavior, scroll behavior, information hierarchy, component philosophy
- Everything that is *independent of pages*
- This document and `index.html` (Theme Prototype) are the outputs
- **Current question:** *"Does this strengthen the Univens Experience System?"*

### Phase 03 — Design System (Next)
**Output:** Grid, typography specs, color tokens, component library, iconography, image guidelines, cursor specs, motion tokens
- Translate philosophy into precise specifications
- Token-based, build-step-ready

### Phase 04 — Theme Prototype (In Progress)
**Output:** `index.html` — a single sandbox page
- No concern about sitemap, navigation, or SEO
- Purpose: Validate *"Does this feel like Univens?"*
- Every UI in the sandbox expresses a system behavior, not a page layout

### Phase 05 — Information Architecture (Future)
**Output:** Sitemap, page hierarchy, navigation model, content structure
- Only after the experience is validated

### Phase 06 — Content Architecture (Future)
**Output:** Homepage, About, Services, Work, Insights, Contact
- Apply the system consistently across all pages

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

**Why this works:** The `.stage` is `position: fixed` so its content never moves. The `#spacer` div (inline `height: 1000vh` = 8 slides × ~125vh scroll budget each) creates real scroll distance. JavaScript maps `window.scrollY` through the spacer to compute which section is active.

**Critical CSS:**
```css
.stage{position:fixed;top:0;left:0;width:100%;height:100vh;overflow:hidden;z-index:2}
.section-block{position:absolute;inset:0;top:clamp(80px,10vh,110px);display:flex;align-items:flex-start;padding:clamp(80px,10vh,180px) clamp(40px,8vw,120px);opacity:0;pointer-events:none;transition:opacity .7s cubic-bezier(.22,1,.36,1)}
.section-block.active{pointer-events:auto}
.section-block .inner{max-width:min(92vw,1100px);width:100%;position:relative}
.section-block .inner.inner-right{margin-left:auto;max-width:min(92vw,800px);padding-left:clamp(40px,8vw,120px)}
.section-block .inner.inner-left{margin-right:auto;max-width:min(92vw,800px);padding-right:clamp(40px,8vw,120px)}
#spacer{pointer-events:none}
```

### 2.2 Scroll math

```javascript
function smoothFade(t){ return t*t*(3-2*t) } // smoothstep

function updateSlides(){
  const maxScroll = spacer.offsetHeight - window.innerHeight;
  const progress = Math.min(1, window.scrollY / maxScroll);
  scrollProgress = progress; // global for cursor
  const rawIndex = progress * (TOTAL - 1); // TOTAL = 8

  sections.forEach((sec, i) => {
    const diff = rawIndex - i;
    let o;
    if(diff < -.18) o = 0;
    else if(diff < 0) o = smoothFade((diff + .18) / .18);
    else if(diff < .82) o = 1;
    else if(diff < .95) o = 1 - smoothFade((diff - .82) / .13);
    else o = 0;
    sec.style.opacity = o;
    sec.style.pointerEvents = o > .5 ? 'auto' : 'none';
    sec.classList.toggle('active', o > .5);

    const lp = Math.max(0, Math.min(1, (diff + .18) / 1.0));
    const isVisible = o > 0.01;
    sec.querySelectorAll('.phase-a').forEach(el => {
      el.classList.toggle('reveal', isVisible && lp > .02);
    });
    sec.querySelectorAll('.phase-b').forEach(el => {
      el.classList.toggle('reveal', isVisible && lp > .1);
    });
  });
  // Also updates: progress dot position, nav .scrolled class, context memory text, section label
}
```

- **Fade in:** 18vh budget (diff -0.18 → 0), smoothstep
- **Full visibility:** 82vh budget (diff 0 → 0.82)
- **Fade out:** 13vh budget (diff 0.82 → 0.95), smoothstep
- **Phase thresholds:** `lp > 0.02` for `.phase-a`, `lp > 0.1` for `.phase-b`
- **Snap scroll:** Wheel/key snaps to nearest section with 800ms cubic-bezier easing; fast scroll >0.5 px/ms collapses phase delays
- **Local progress (`lp`):** `clamp(0, (diff + 0.18) / 1.0, 1)` — phase reveal timing is relative to the section's full visibility window, not absolute scroll

### 2.3 Preloader

Custom preloader with letter-rise + cycling phrases:
- Fullscreen `var(--bg)` overlay, `z-index: 999999`
- "Univens" reveals letter by letter (0.07s delay per character, `@keyframes riseUpFade`: opacity 0→1, translateY 24px→0)
- After 1.1s, the text clears and cycles through 5 phrases at 800ms intervals:
  - "This is an interactive briefing."
  - "Each viewport answers one question."
  - "Explore. Hover. Click. Learn."
  - "Let's begin."
- Fades out 500ms after last phrase with `.hidden` class (`opacity:0; pointer-events:none`)
- Also hides immediately on tab switch (`visibilitychange`) or page unload (`pagehide`)
- When hidden, triggers `.visible` class on `#vignette` with 2s opacity transition

---

## 3. Design System

### 3.1 Colors
| Token | Value | Usage |
|-------|-------|-------|
| `--bg` | `#F8F9FA` | Page, stage, nav, footer, preloader (warm off-white) |
| `--surface` | `#F4F5F6` | Subtle surface for backgrounds |
| `--text-primary` | `#1A1C1E` | Headings, strong emphasis |
| `--text-secondary` | `rgba(26,28,30,.74)` | Body copy, explanations |
| `--text-tertiary` | `rgba(26,28,30,.48)` | Supporting text, footnotes |
| `--text-muted` | `rgba(26,28,30,.28)` | Labels, timestamps |
| `--text-disabled` | `rgba(26,28,30,.13)` | Minimum contrast elements |
| `--border` | `rgba(26,28,30,.07)` | Dividers, card borders, subtle lines |
| `--border-s` | `rgba(26,28,30,.12)` | Stronger borders, button outlines |
| `--selection` | `rgba(79,86,94,.14)` | Text selection highlight |
| `--decision` | `#4F565E` | Interactive element accent, active state |
| `--decision-hover` | `#434950` | Deeper hover state |
| `--decision-bg` | `rgba(79,86,94,.07)` | Background tint for decision elements |
| `--connected` | `#6B7B8A` | Secondary interactive color |
| `--connected-bg` | `rgba(107,123,138,.07)` | Background tint for secondary elements |
| `--focus-ring` | `#6B7B8A` | Focus state ring |
| `--nav-bg` | `rgba(248,249,250,.85)` | Nav background with scroll |
_(cursor colors removed — uses `#fff` with `mix-blend-mode:difference` instead)_

Colors derive from `#1A1C1E` (dark gray, not pure black) with controlled alpha. `#4F565E` and `#6B7B8A` are the interactive accent palette.

### 3.2 Typography

```
font-family: 'Inter Tight', sans-serif (weights 400, 450) — body, UI copy
font-family: 'Geist', sans-serif (weights 500, 600, 700) — headings, nav, buttons
font-family: 'Instrument Serif', serif (weight 400, <em> only)
font-family: 'Fragment Mono', monospace (weights 400, 500) — labels, meta, section numbers
```

| Class | Size | Weight | Usage |
|-------|------|--------|-------|
| `.hero-headline` | `clamp(28px,3.4vw,60px)` | 600 | Hero main statement |
| `.section-heading` | `clamp(22px,2.2vw,40px)` | 500 | Section headings |
| `.section-subhead` | `clamp(12px,.75vw,15px)` | 400 | Body copy, explanations |
| `.section-label` | `9px` | 500 | Mono section labels (uppercase) |
| `.btn` | `12px` | 500 | Primary button |
| `.btn-ot` | `11px` | 400 | Secondary CTA |
| `.metric-val` | `clamp(18px,2.5vw,32px)` | 600 | Metric number |
| `.metric-lbl` | `10px` | 500 | Metric label (mono, uppercase) |
| `nav .logo` | `clamp(13px,.9vw,15px)` | 500 | Nav logo (Geist) |
| `nav .cta` | `clamp(12px,.8vw,14px)` | 500 | Nav CTA (Geist) |

**Typography rules:**
- Geist for headings and UI (modern, geometric sans). Inter Tight for body (readable, warm).
- Fragment Mono replaces IBM Plex Mono for labels, section numbers, meta text (smaller x-height, more character)
- Font weights 100–300 never used
- Weight 400: body copy, explanations
- Weight 450: preferred body weight (Inter Tight only)
- Weight 500: UI labels, nav, buttons, section labels
- Weight 600: hero, metric values, strong headings
- Never italicize Inter Tight — `<em>` uses Instrument Serif with `font-style:normal`
- `text-wrap:balance` on headings
- Uppercase allowed only on: section labels (`section-label`), metrics, tiny metadata
- Uppercase forbidden in: paragraphs, buttons, headlines
- Premium contrast: display text (Geist) has bold presence against muted body (Inter Tight, `--text-tertiary`)

### 3.3 Spacing
- **Section padding:** `clamp(80px,10vh,180px)` vertical, `clamp(40px,8vw,120px)` horizontal — generous Apple-style breathing room
- **Inner max-width:** `min(92vw,1100px)` default; asymmetry variants `.inner-right` (pushed right, max 800px) and `.inner-left` (pushed left, max 800px)
- **Interactive component gaps:** `.ewall` gap `clamp(28px,3vw,44px)`, `.ophilo` gap `clamp(32px,4vw,64px)`
- **Phase reveal translate:** `24px` (was 12px) for more dramatic entrance
- **Word reveal blur:** `10px` blur on `.ch` elements, 1.2s transition

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
| Cursor | 1080 | Dark dot + gap-ring, state machine, spring physics, hidden on mobile |
| Menu overlay | 200 | Fullscreen nav menu with section links + CTA |
| Nav | 100 | Logo (left) + hamburger (right), backdrop-blur background |
| Context memory | 99 | Previous section's key sentence for conversational continuity |
| Letterbox | 50 | Fixed top/bottom cinematic bars with gradient |
| Stage (text) | 2 | Fixed container for all section content |
| Host system | 98 | Bottom-right FSM-driven contextual messenger with backdrop blur |
| Vignette | 1 | Multi-stop radial gradient overlay |
| Footer | 3 | Contact info, `position:relative;background:var(--bg)` |
| Noise overlay | 0 | SVG film grain, `opacity:.02`, `mix-blend-mode:multiply` |

### 4.2 Content sections (8 viewports)

| # | Section | Content structure |
|---|---------|-------------------|
| 0 | Hero (What exactly is Univens?) | Centered headline + execution network SVG (6 orbiting capability nodes) + CTAs |
| 1 | Thinking (How do they think?) | Decision framework: 4 clickable stages (Understand→Prioritize→Execute→Improve) with decisions per stage; `.inner-right` alignment |
| 2 | Proof (Why should I trust them?) | Evidence wall with filterable client logos (8 clients, 3 industries) + metrics |
| 3 | Capabilities (Can they solve my problem?) | Business Operating System: 6 capability nodes in orbital layout around a center; `.inner-left` alignment |
| 4 | Execution (What does execution look like?) | Project file with 6 tabs (Objective→Situation→Decision→Execution→Outcome→Metrics), 2 projects toggle |
| 5 | Relationship (What is working with Univens like?) | Relationship timeline with 6 expandable events; `.inner-right` alignment |
| 6 | About (Who are these people?) | Operating philosophy: 4 clickable principles with content panel |
| 7 | Conversation (Should I talk to them?) | Centered input field + CTAs + 24h response note |

### 4.3 Content architecture (no narrative flow system)

Each section uses a flexible structure with `.inner` containers and phase-based reveals:

```html
<div class="section-block vp-xxx" role="region">
  <div class="inner">
    <div class="phase-a"><span class="section-label">Label</span></div>
    <div class="phase-a">
      <h2 class="hero-headline">Heading or headline text.</h2>
      <p class="section-subhead">Supporting body text.</p>
    </div>
    <div class="phase-b">
      <!-- Primary UI component (SVG, grid, tabs, etc.) -->
    </div>
    <div class="phase-b">
      <a href="#" class="btn">CTA →</a>
    </div>
  </div>
</div>
```

- `.inner` default: centered, `max-width: min(92vw,1100px)`
- `.inner.inner-right`: pushed right with `margin-left:auto`, `max-width: 800px`, left padding for visual balance
- `.inner.inner-left`: pushed left with `margin-right:auto`, `max-width: 800px`, right padding for visual balance
- `.phase-a`/`.phase-b`: `opacity:0; transform:translateY(24px)` → `.reveal` sets `opacity:1; transform:translateY(0)`
- No rigid flow structure; each section arranges its content freely within `.inner`
- Asymmetry alternates sections for visual rhythm: centered → right → centered → left → centered → right → centered → centered

### 4.4 Navbar

```
[star] Univens                       [Let's talk →]  [☰]
```

- Always visible, no hide/show on scroll; `.scrolled` class adds border and more opaque backdrop-blur on scroll
- `pointer-events:none` on nav, `pointer-events:auto` on children (logo, CTA, hamburger)
- Hamburger menu opens overlay with 8 section links + CTA; hamburger lines animate to ✕
- `.menu-overlay` with `var(--bg)` background, `z-index:200`
- Backdrop-blur: 18px default, 24px scrolled; background opacity shifts `.55` → `.82`
- Desktop: logo (left), CTA button + hamburger (right). Mobile: logo (left), hamburger only (right); CTA moves inside menu overlay.

### 4.5 Preloader

- Fullscreen `var(--bg)` overlay with `z-index: 999999`
- "Univens" animates letter by letter (riseUpFade)
- Cycles through 5 brand phrases at 700ms intervals
- `transition: opacity .5s ease` for fade out
- Auto-hides on `visibilitychange` and `pagehide`

---

## 5. Animation System

### 5.1 Phase reveals (JS-driven `.reveal` class)

- `.phase-a` / `.phase-b` base: `opacity:0; transform:translateY(24px); transition:opacity .7s cubic-bezier(.22,1,.36,1), transform .7s cubic-bezier(.22,1,.36,1)`
- `.phase-a.reveal` / `.phase-b.reveal`: `opacity:1!important; transform:translateY(0)!important`
- Thresholds: `lp > 0.02` for `.phase-a`, `lp > 0.1` for `.phase-b`
- Gated by `isVisible = o > 0.01` flag to prevent premature removal
- `!important` guarantees override against competing opacity rules
- Escape hatch: fast scroll (>0.5 px/ms) collapses delays to 0 so phases appear simultaneously

### 5.2 Word-by-word blur-in (`.ch` elements)
- Each `.ch` span starts with `opacity:0; filter:blur(10px)`
- When parent `.section-block` gets `.active` class, transitions to `opacity:1; filter:blur(0)`
- Duration: `1.2s` with `cubic-bezier(.22,1,.36,1)` spring easing
- Stagger: `120ms` delay between each word (via inline `style="transition-delay:...s"`)
- `.active` is additive (never removed) — words that have been revealed stay revealed even when section fades

### 5.3 Section transitions (scroll-driven)
- Each `.section-block` has `transition:opacity .7s cubic-bezier(.22,1,.36,1)` — this is the CSS transition; the JS `updateSlides()` sets `style.opacity` every frame, and the CSS transition smooths the visual change
- Smoothstep easing (`t*t*(3-2*t)`) applied to the fade-in/fade-out windows

### 5.4 Cursor — modern premium redesign
Clean, minimal cursor system inspired by modern design patterns (Apple, Stripe). White dot + ring with `mix-blend-mode:difference` for universal visibility against any background.
- **Dot**: 5px white circle with subtle glow (`box-shadow: 0 0 8px rgba(255,255,255,.15)`), `mix-blend-mode:difference`, instant mouse-follow (not spring-positioned)
- **Ring**: 28px circle, 1.2px white border at 65% opacity, `mix-blend-mode:difference`, follows behind dot with spring physics
- **Ring.hover**: Expands to 44px on interactive elements (buttons, links, stages, topics, nodes, tabs), fills with 7% white, border brightens to 90%
- **Ring.input**: Shrinks to 18px at 30% opacity when input field is focused
- **Label**: Contextual text label (Inter Tight 11px, white) positioned above-left of cursor, dark pill background (`rgba(26,28,30,.72)`) with 6px backdrop-blur
- **Spring physics**: `SK=0.12` (stiffness), `SD=0.70` (damping) — creamy, premium follow
- **Idle blink**: Dot subtly shrinks after 15s of inactivity (4s cycle)
- **Particle trail**: 2px white dots with `mix-blend-mode:difference`, spawned during active motion, 0.4s fade-out lifetime, pooled
- **Magnetic pull (ring only)**: Gentle 7px attraction toward interactive elements within 45px. Only the ring is pulled (spring target offset) — the dot stays precisely on the mouse for accurate clicking. Creates a visual "anticipation" stretch effect.
- **Engagement arc**: Ring tracks section exploration via `H.p.sectionInts`. Shows a subtle white conic-gradient arc that fills as the user explores more interactive elements in the current section. Resets on section change.
- **Idle hints**: After 6s of inactivity with no hover, shows a section-relevant exploration cue (e.g., "Click a stage →" for V02, "Filter by industry →" for V03) at bottom-center of viewport. Hides on mouse move or after 5s display.
- **Removed features**: Gap-ring completion (replaced by engagement arc), breathing animation, scroll velocity fade, section transition pulse, morph-to-arrow/pencil states, element-specific scale variants

### 5.5 Preloader animation
- `@keyframes riseUpFade`: `opacity:0; transform:translateY(24px)` → `opacity:1; transform:translateY(0)`
- `0.25s` duration with `cubic-bezier(.22,1,.36,1)` spring easing
- Letter-by-letter delay: `0.07s` per character
- Cycling phrases: plain text swap via `textContent` (no animation), 800ms intervals

### 5.6 Host intelligence FSM
Finite state machine with states: ARRIVED → WELCOME → HOVERING / EXPLORING / RETURNING / DEEP_DIVE / READY_TO_LEAVE → OBSERVING
- State transitions triggered by: `hover` (mouseenter interactive element), `click` (on interactive element), `section` (scroll to new section), `tick` (500ms interval)
- Each section has customized messages for welcome, hover context, explore acknowledgment, return, deep dive
- Interaction tracking (`H.p`) records which stages/caps/topics/events/projects the user has explored, used to decide when to offer DEEP_DIVE

---

## 6. Background layers (rendering order, bottom to top)

1. **Noise** (`#noise`, `z-index:0`) — SVG film grain at 2% opacity, `mix-blend-mode: multiply`
2. **Vignette** (`#vignette`, `z-index:1`) — Multi-stop radial gradient overlay, fades in after preloader
3. **Stage** (`z-index:2`) — All section content, fixed position, holds `.section-block` × 8
4. **Letterbox** (`#letterbox`, `z-index:50`) — Fixed top/bottom cinematic bars, gradient background, hidden on mobile
5. **Host system** (`#host`, `z-index:98`) — Bottom-right FSM-driven contextual messenger, backdrop-blur, padding
6. **Context memory** (`#ctx-memory`, `z-index:99`) — Previous section summary, top-left
7. **Nav** (`nav`, `z-index:100`) — Logo + hamburger, 18px–24px backdrop-blur, `.scrolled` adds border
8. **Menu overlay** (`z-index:101`) — Fullscreen nav menu with 8 section links
9. **Cursor** (`#cursor`, `z-index:1080`) — Spring physics dot + gap-ring, particle trail, contextual labels
10. **Preloader** (`#custom-preloader`, `z-index:999999`) — Fullscreen overlay, removed after letter/phrase animation

---

## 7. Constraints (must NOT be changed)

- **No position:fixed alternative** — Must use `.stage` (fixed) + `#spacer` pattern
- **No blur/translateY/transform on scroll transitions** — Section-level opacity only
- **No text movement on scroll** — Only opacity fades between sections
- **Sections must not visibly overlap** — Only one section visible at any scroll position
- **Fixed-stage architecture is required** — Do not switch to natural scroll or sticky-based approaches
- **Single HTML file** — All CSS and JS must remain embedded
- **Font stack** — Inter Tight (body/UI), Geist (headings/UI), Instrument Serif (`<em>` only), Fragment Mono (labels/meta)
- **No font weight below 400 or above 700**
- **No italic Inter Tight** — `<em>` must use Instrument Serif with `font-style:normal`
- **Color system** — Background `#F8F9FA` (warm off-white), text `#1A1C1E` (dark gray). Interactive accent: `#4F565E` (decision), `#6B7B8A` (connected). No pure black/white.
- **Custom cursor** — Must be present on desktop (≥1024px), hidden on mobile (≤1023px); spring physics with gap-ring, magnetic pull, contextual labels
- **Phase system** — Phase-a at `lp > 0.02`, Phase-b at `lp > 0.1`, gated by `isVisible`
- **Background layers** — Noise at z-index 0 (SVG film grain, 2% opacity, multiply blend mode); Vignette at z-index 1 (multi-stop radial gradient); Letterbox cinematic bars at z-index 50
- **Host intelligence** — FSM-based bottom-right system with contextual messaging per section, must remain
- **Preloader** — Must remain light mode (`var(--bg)` background), letter-rise for "Univens" + cycling phrases; must also hide on `visibilitychange`/`pagehide`
- **8 sections** — Hero, Thinking, Proof, Capabilities, Execution, Relationship, About, Conversation (in that order)
- **Snap scroll** — Wheel/key snaps to nearest section with 800ms cubic-bezier; 550ms fallback on reduced motion

---

## 8. Preferences (can be adjusted with care)

- Preloader phrase content and timing
- Phase threshold values (`lp > 0.02` / `lp > 0.1`)
- Section copy and content
- Button hover effect depth, shadow intensity
- Typography scale if the premium feel is maintained
- Grid column counts and breakpoints
- Cursor behavior parameters (spring constants, magnetic radius)
- Snap scroll duration and easing
- Footer content and structure
- Asymmetry alignment: which sections use `.inner-right`/`.inner-left`
- Box-shadow values on interactive hover states

---

## 9. File structure

```
univens/
├── index.html    # Theme Prototype / Experience Sandbox (~1268 lines)
│   ├── <head>    # Fonts (Google Fonts: Geist, Inter Tight, Instrument Serif, Fragment Mono), inline CSS (~268 lines)
│   ├── <body>    # Preloader, cursor, noise, letterbox, vignette, host, nav, menu, stage (8 sandbox sections), spacer, footer
│   └── <script>  # Cursor state machine (spring physics), host FSM, 8 interactive components, scroll engine, snap, preloader
├── theme.md      # This document — Experience System Design (~810 lines)
│                  # Purpose: codify the system, not spec the homepage
```

---

## 10. Common pitfalls for AI agents

1. **Don't remove the fixed-stage pattern** — It's the core UX. Natural scroll breaks the "text stays fixed" constraint.
2. **The `.reveal` class is toggled every frame** — `updateSlides()` runs on every rAF tick and sets/removes `.reveal` based on current scroll position. `.active` is *additive* (never removed) — `.reveal` is *toggled*.
3. **Phase thresholds are gated by `isVisible`** — Without `isVisible = o > 0.01`, `.reveal` would be removed too early as section opacity crosses zero.
4. **`!important` on `.reveal` is intentional** — It guarantees override against competing opacity rules from transition base states.
5. **`.section-block` selector prefix on phase rules** — Rules use `.section-block .phase-a` not `.inner .phase-a` to catch elements outside `.inner`.
6. **Preloader must hide on `visibilitychange`** — Without this, the preloader could persist when returning to the tab via browser cache.
7. **8 sections, not 9** — Sections are: Hero, Thinking, Proof, Capabilities, Execution, Relationship, About, Conversation. The `ST` array in JS must match.
8. **Tag mismatches break all JS** — A single unclosed `<div>` or `<span>` can halt the entire script. Verify tag balance after edits.
9. **Section blocks use class-based targeting** — Each section has a unique class (`vp-hero`, `vp-thinking`, `vp-proof`, `vp-capabilities`, `vp-execution`, `vp-relationship`, `vp-about`, `vp-conversation`) for section-specific CSS overrides and distinct atmospheric treatments. All sections use `.inner` (centered) — `.inner-right`/`.inner-left` asymmetry classes are deprecated.
10. **The cursor has spring physics** — Do NOT replace with lerp. The spring constants (`SK=0.14, SD=0.68`) produce the organic follow-behind motion. Magnetic pull activates within 60px of interactive elements.
11. **Host FSM has its own section index** — `H.section` is synced with the `ST` array via monkey-patched `updateSlides()`. Adding or removing sections requires updating both `ST` and the `MSGS` array.
12. **Section blocks use `pointer-events:auto/none`** — `pointer-events` is set to `auto` when opacity > 0.5, `none` otherwise. This prevents interaction with invisible sections.
13. **CSS custom properties are live** — The `:root` token system (`--decision`, `--connected`, `--text-primary`, etc.) is used by JS-generated SVG and components. Renaming a token breaks interactive elements.

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

### Session 21 — Smart cursor: context-aware with magnetic pull + engagement arc
- **Upgraded to "Smart Cursor"**: Rebuilt cursor with section awareness, magnetic intelligence, and engagement tracking
- **Magnetic pull**: Ring is gently attracted (7px max) toward interactive elements within 45px. Dot stays precise on mouse for accurate clicking — only the ring's spring target is offset, creating a visual anticipation stretch
- **Engagement arc**: Ring shows a subtle conic-gradient white arc that fills (0→360°) as the user explores interactive elements in the current section. Uses `H.p.sectionInts` data. Resets per section.
- **Idle hints**: After 6s of no hover activity, a section-relevant cue appears at bottom-center (e.g., "Filter by industry →", "Expand an event →"). Each of the 8 sections has a tailored hint. Displays for 5s then fades.
- **Target caching**: `cacheTargets()` rebuilds interactive element positions on scroll/resize for accurate magnetic pull calculations
- **Section tracking**: `lastSec` state tracks section changes to reset engagement and idle hint state

### Session 23 — Experience System reframe (strategic reset)
- **Fundamental reframe**: User identified that we were optimizing for "launching a website" when we should be optimizing for "creating a design language." The project was reset to focus on the **Univens Experience System** — the visual, motion, interaction, typography, color, layout, host, cursor, scroll, and information philosophy from which all pages will be composed.
- **Phase Architecture defined**: 6 phases — Brand Core (complete) → Experience System (current) → Design System (next) → Theme Prototype (in progress) → Information Architecture (future) → Content Architecture (future).
- **New evaluation question**: Every idea is now evaluated against: *"If this becomes part of the Univens design language, should every page inherit this behavior?"* If yes, it's a system decision. If no, it's a page decision.
- **`index.html` redefined**: No longer "homepage" — now **Theme Prototype** / **Experience Sandbox**. Its purpose is to validate system behaviors, not page content, CTAs, SEO, or navigation.
- **theme.md updated**: Project Identity reframed as Experience System Design. Phase Architecture added as section 1b. Asymmetric layout marked as deprecated. File structure comments updated. Session 23 appended.

### Session 22 — Brand website transformation: from product UI to cinematic chapters
- **Major critique addressed**: User identified the site still felt like "product UI" / "software dashboard" rather than a "premium brand website". Initiated section-by-section visual transformation to make each feel like a distinct cinematic chapter.
- **Hero (V00)**: Restructured HTML — network SVG moved to absolute-positioned background (z-index:-1, centered, 60s orbit rotation, opacity:0.1). Giant typography with atmospheric radial gradient. Clean centered layout with minimal CTA at 50% opacity.
- **Thinking (V01)**: Removed `.inner-right` (returned to centered `.inner`). Added proper heading + subhead. Exhibition-style horizontal journey with large typographic numbers (28–52px), no borders/boxes, elegant underline on hover, active state shows full opacity, inactive at 0.3.
- **Proof (V02)**: Added proper heading + subhead. Editorial logo wall with text-only filters, massive typographic metrics (28–56px), no borders, generous spacing.
- **Capabilities (V03)**: Removed `.inner-left` (returned to centered `.inner`). Added heading + subhead. Organic floating nodes with individual CSS float animations (7–9s cycles), subtle shadow glow, no dashed connection lines, hover stops animation with scale.
- **Execution (V04)**: Added heading + subhead. Editorial project file with slash-separated tabs, larger title and content metrics (22–40px values).
- **Relationship (V05)**: Removed `.inner-right` (returned to centered `.inner`). Added heading + subhead. Cinematic timeline with 9px dot markers, more whitespace, cleaner labels.
- **About (V06)**: Added heading + subhead. Typographic philosophy with large topic labels (18–28px), no borders, clean vertical rhythm.
- **Conversation (V07)**: Underline-style input (no box), generous sizing, clean typography.
- **Chapter atmospheres**: Each section gets a unique radial gradient `::after` overlay (`.vp-hero` ellipse at 50%40%, `.vp-thinking` at 60%50%, etc.) at 1.5–2.5% opacity with subtly different color tones — making each feel like a different "chapter" rather than a uniform template.
- **Deprecated asymmetry classes**: `.inner-right`/`.inner-left` no longer used on any section. All 8 sections now use centered `.inner`.
- **theme.md updated**: Director's Briefs layout columns corrected, pitfalls updated for asymmetry deprecation.

### Session 20 — Modern cursor redesign
- **Complete cursor visual overhaul**: Replaced dark gap-ring cursor with premium white dot+ring system using `mix-blend-mode:difference`
- **Dot**: 5px white circle with subtle glow, instant mouse-follow (no spring lag)
- **Ring**: 28px circle, 1.2px white border (65% opacity), spring-physics follow (SK=0.12, SD=0.70)
- **States**: `.hover` (expands to 44px, fills 7% white, border to 90%) and `.input` (shrinks to 18px, 30% opacity)
- **Label**: Inter Tight 11px on dark pill (`rgba(26,28,30,.72)`) with 6px backdrop-blur, positioned above-left
- **Particles**: 2px white dots with mix-blend-mode:difference, 0.4s lifetime, pooled, spawned during motion
- **SIMPLIFIED JS**: Removed gap-ring (conic-gradient), magnetic pull, breathing, scroll velocity fade, section transition pulse, morph-to-arrow/pencil, element-specific scale variants, cacheTargets, `isP` tracking — reduced from ~170 lines to ~55 lines
- **Cleaned up**: Removed unused CSS vars `--cursor-dot`, `--cursor-ring`, `--cursor-halo` from `:root`
- **Idle blink**: Reduced threshold to 15s (was 20s)

### Session 19 — Premium visual polish: whitespace, typography, asymmetry, depth
- **Whitespace expansion**: `section-block` vertical padding increased from `clamp(64px,7vw,110px)` to `clamp(80px,10vh,180px)` for more breathing room
- **Dramatic typography**: Hero headline boosted to `clamp(28px,3.4vw,60px)` with tighter tracking (`-.03em`). Section headings increased to `clamp(22px,2.2vw,40px)`. Body text (`section-subhead`) reduced and softened to `--text-tertiary` for greater contrast
- **Asymmetry introduced**: Added `.inner-right`/`.inner-left` modifiers. V02 (Thinking) pushed right, V04 (Capabilities) pushed left, V06 (Relationship) pushed right — breaking the predictable centered column
- **Cinematic depth**: Nav backdrop-blur increased (18px default, 24px scrolled). Vignette enhanced with multi-stop radial gradient. Letterbox bars use gradient. Button/interactive hover states now include subtle box-shadows. `.section-block` ambient glow expanded to 100vw/80vh
- **Host system**: Added backdrop blur and padding for depth layering
- **Internal spacing**: `.ewall` gap increased, `.ophilo` gap increased for more generous internal whitespace

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

## 13. Director's Briefs (Sandbox Sections)

These sections exist in the Theme Prototype (`index.html`) to validate system behaviors — scroll, cursor, host, phase reveals, interaction patterns. They are **not** page content. The specific copy and visuals here serve the system, not the final information architecture.

The natural narrative arc across the 8 sandbox sections: what → how → proof → can they → show me → what's it like → who → let's talk.

### V00 — Hero (What exactly is Univens?)
| Field | Value |
|---|---|
| **Narrative Purpose** | Pure atmosphere — introduce Univens with cinematic typography and subtle background motion |
| **Primary Emotion** | Curiosity, clarity |
| **Headline** | "Execution for growing businesses." |
| **Visual** | 6-node execution network SVG as atmospheric background (absolute, z-index:-1, 60s orbit rotation, 0.1 opacity) |
| **Action** | Start a Conversation → |
| **Phase-a** | Section label + headline + subhead |
| **Phase-b** | Network SVG (background) + CTA |
| **Layout** | Centered (`.inner` default), atmospheric radial gradient chapter treatment |
| **CSS treatment** | `vp-hero` — giant typography, subtle SVG orbit, `::after` radial gradient at 50%40% |

### V01 — Thinking (How do they think?)
| Field | Value |
|---|---|
| **Narrative Purpose** | Exhibition-style showcase of structured decision-making framework |
| **Primary Emotion** | Trust in process |
| **Headline** | "We use a four-stage decision framework." |
| **Visual** | 4 clickable stages (Understand→Prioritize→Execute→Improve) with per-stage decision lists, horizontal exhibition layout |
| **Action** | Start a Conversation → |
| **Phase-a** | Section label + heading + subhead |
| **Phase-b** | Decision framework + CTA |
| **Layout** | Centered (`.inner` default), exhibition atmosphere |
| **CSS treatment** | `vp-thinking` — large typographic numbers (28–52px), no borders/boxes, underline hover, radial gradient at 60%50% |

### V02 — Proof (Why should I trust them?)
| Field | Value |
|---|---|
| **Narrative Purpose** | Editorial evidence wall — trust through verifiable client outcomes |
| **Primary Emotion** | Confidence |
| **Headline** | "Trust is built on outcomes." |
| **Visual** | Filterable client grid (Disney, Amazon, Nickelodeon, Viacom18, Spruce, Fireblaze, OK Pharma, Zesh) + text-only industry filters + live typographic metrics |
| **Action** | Start a Conversation → |
| **Phase-a** | Section label + heading + subhead |
| **Phase-b** | Evidence wall + metrics + CTA |
| **Layout** | Centered (`.inner` default), editorial atmosphere |
| **CSS treatment** | `vp-proof` — no borders on filters/items, text-only interaction, massive metrics (28–56px), radial gradient at 40%60% |

### V03 — Capabilities (Can they solve my problem?)
| Field | Value |
|---|---|
| **Narrative Purpose** | Organic capability system — interconnected nodes with subtle motion |
| **Primary Emotion** | Understanding |
| **Headline** | "Your business is a system. So is our approach." |
| **Visual** | Business Operating System — 6 floating orbital capability nodes (Infrastructure, Brand, Marketing, Operations, AI, Technology) with individual CSS float animations, hover stops animation with scale |
| **Action** | Start a Conversation → |
| **Phase-a** | Section label + heading + subhead |
| **Phase-b** | BOS map + detail panel + CTA |
| **Layout** | Centered (`.inner` default), organic atmosphere |
| **CSS treatment** | `vp-capabilities` — floating nodes (7–9s float cycles), subtle shadow glow, no dashed lines, radial gradient at 50%50% |

### V04 — Execution (What does execution look like?)
| Field | Value |
|---|---|
| **Narrative Purpose** | Editorial project documentary — concrete case with real metrics |
| **Primary Emotion** | Confidence in delivery |
| **Headline** | "Real project, real metrics." |
| **Visual** | Project file with 6 slash-separated tabs (Objective, Situation, Decision, Execution, Outcome, Metrics), 2 project toggle, live metric values (22–40px) |
| **Action** | Start a Conversation → |
| **Phase-a** | Section label + heading + subhead |
| **Phase-b** | Project file + toggle + CTA |
| **Layout** | Centered (`.inner` default), editorial atmosphere |
| **CSS treatment** | `vp-execution` — slash-separated tabs, larger metrics (22–40px), editorial typography, radial gradient at 30%50% |

### V05 — Relationship (What is working with Univens like?)
| Field | Value |
|---|---|
| **Narrative Purpose** | Cinematic relationship arc — show the partnership journey, not just deliverables |
| **Primary Emotion** | Connection, transparency |
| **Headline** | "A partnership, not a transaction." |
| **Visual** | Relationship timeline — 6 clickable events from Initial Call to Final Handover, each with expandable detail, larger 9px dot markers |
| **Action** | Start a Conversation → |
| **Phase-a** | Section label + heading + subhead |
| **Phase-b** | Timeline + CTA |
| **Layout** | Centered (`.inner` default), cinematic atmosphere |
| **CSS treatment** | `vp-relationship` — 9px dot markers, generous whitespace, cleaner labels, radial gradient at 70%40% |

### V06 — About (Who are these people?)
| Field | Value |
|---|---|
| **Narrative Purpose** | Typographic philosophy — define values through concrete principles, no interface elements |
| **Primary Emotion** | Alignment |
| **Headline** | "You're hiring a philosophy, not an office." |
| **Visual** | 4 clickable operating principles (What we optimize first, What accountability means, How we make decisions, What we refuse to compromise) with content panel |
| **Action** | Start a Conversation → |
| **Phase-a** | Section label + heading + subhead |
| **Phase-b** | Philosophy topics + content + CTA |
| **Layout** | Centered (`.inner` default), typographic atmosphere |
| **CSS treatment** | `vp-about` — large topic labels (18–28px), no borders, underline active state, clean vertical rhythm, radial gradient at 50%60% |

### V07 — Conversation (Should I talk to them?)
| Field | Value |
|---|---|
| **Narrative Purpose** | Clean invitation — underline-style input, minimal UI, low-friction starting point |
| **Primary Emotion** | Readiness, openness |
| **Headline** | "Let's begin with your objective." |
| **Visual** | Input field with cycling placeholders ("We're expanding." / "We're stuck." / "We're rebuilding." / "We're exploring."), underline style |
| **Action** | Start the Conversation → |
| **Phase-a** | Section label + heading + subhead |
| **Phase-b** | Input field + CTA + 24h response note |
| **Layout** | Centered (`.inner` default, max-width: 32em), invitation atmosphere |
| **CSS treatment** | `vp-conversation` — underline input (no box), generous sizing, clean typography, centered invitation, radial gradient at 50%50% |
