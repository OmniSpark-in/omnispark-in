# Omnispark Website — Full Context

## Project overview

**omnispark.in** is the marketing website for Omnispark, a small independent EdTech studio building digital learning tools for Indian kids (Classes 1–5). The site promotes two things:
1. **GyanQuest** — a free Android app (offline-first, NCERT-aligned, no ads, parent dashboard)
2. **Free printable worksheets** — downloadable PDFs organised by subject, class, and topic

Domain: omnispark.in  
Stack: Pure HTML/CSS/JS (no framework, no build step). Single static file: `index.html`.

---

## File structure

```
index.html          ← Main marketing page (fully built, all sections present)
privacy.html        ← Privacy policy page (styled, live)

assets/
  images/
    gyanquest-icon-48.png    ← App icon used in nav CTA + hero "Get the App" button
    gyanquest-icon-192.png   ← Larger app icon used in app card + nudge strip
  worksheets/               ← Intended home for real PDFs (currently empty; all
                               worksheet entries are placeholders in the JS data)
    {Subject}/{Class}/{Topic}/{filename}.pdf   ← future convention (see below)

node_modules/         ← Playwright (dev testing only; NOT deployed)
package.json          ← Created by Playwright install; NOT part of the site
```

**How to add a real worksheet PDF:**
1. Place the file at `assets/worksheets/{Subject}/{Class}/{Topic}/{filename}.pdf`  
   e.g. `assets/worksheets/Math/Class 1/Counting/counting-1-10.pdf`
2. In the `WORKSHEETS` array in `index.html`, find the matching sheet entry.
3. Change `file: null` → `file: 'assets/worksheets/Math/Class 1/Counting/counting-1-10.pdf'`

That one change turns the "Coming soon" row into a live download link. No HTML or CSS changes needed.

---

## index.html — section by section

### Design tokens (CSS variables)
```css
--orange:  #FF6B35
--gold:    #FFB800
--dark:    #1C1320
--cream:   #FFF8F0
--muted:   #5a4f5c
--white:   #ffffff
```

### Fonts
- **Nunito** (Google Fonts) — body text, nav, buttons
- **Playfair Display** (Google Fonts) — headings, logo word, hero h1
- Weights: Nunito 400/500/600/700/800; Playfair Display 600/700 + italic

### Icons
Tabler Icons webfont via CDN (`@tabler/icons-webfont`).  
Used as `<i class="ti ti-{icon-name}"></i>`.  
Icons in use: `ti-sparkles`, `ti-brand-google-play`, `ti-wifi-off`, `ti-ad-off`, `ti-users`, `ti-school`, `ti-chevron-down`, `ti-download`

---

### NAV (`#main-nav`)
- Fixed/sticky, `z-index: 300`
- Transparent on hero; becomes **frosted glass** (`rgba(255,248,240,0.9)` + `backdrop-filter: blur(14px)`) after 40 px scroll
- Class `.scrolled` toggled by JS on the `scroll` event
- Logo: orange rounded square "O" + "Omnispark" in Playfair Display
- Desktop links: **Worksheets → `#worksheets`**, **Kindergarten → `#kindergarten`**, **About → `#about`**
- CTA button: "Get GyanQuest" with app icon (links to `#` — needs real Play Store URL)
- Mobile: nav links + CTA hidden; hamburger shown → mobile drawer (`#mobile-drawer`) with same links
- Hamburger animates to X via CSS transforms on 3 `<span>` elements

**JS functions:**
- `toggleNav()` — hamburger `onclick`; toggles `.open` on drawer and hamburger
- `closeNav()` — called by drawer link `onclick`; closes drawer

---

### HERO (`#hero`)
- Full viewport height, `var(--dark)` background
- Decorative layers (all `pointer-events: none`):
  - `.hero-grid-bg` — subtle white grid via `background-image` linear-gradients
  - `::before` — two radial orange/gold glows
- Badge: "✦ Indie EdTech · Made in India"
- `h1`: "Your child's learning companion for *Classes 1–5*" (italic "Classes 1–5" in gold)
- Subtext: free worksheets + app, NCERT-aligned, bilingual, offline-first
- Two CTA buttons:
  - `.btn-fill` "Download Worksheets" → `#worksheets`
  - `.btn-ghost` "Get the App" → `#` (needs Play Store URL)
- Scroll hint: animated mouse icon + "SCROLL" at bottom centre

---

### APP SHOWCASE (`#app`)
White background. Two-column grid (`.app-grid`): left = app card, right = feature list.

**App card (`.app-card`):**
- App icon image (192 px) + type label "Free EdTech App" + name "GyanQuest" in Playfair Display
- Description text
- **Subject pills** (`.pills`):
  - Active subjects: `Math`, `Science`, `GK`, `English`, `Olympiad` — orange `.pill`
  - Coming-soon: `More subjects coming soon` — muted `.pill.pill-soft`
- **Olympiad status line** (`.app-olympiad-note`):  
  "Olympiad: Math available · Soon · Science, GK & English coming soon"  
  The "Soon" word is wrapped in `.soon-tag` for the muted badge treatment.
- Buttons: "Get on Google Play" (dark filled) + "Learn more" (dark outline); both link to `#`

**Feature list (`.feat-list`) — four rows:**
1. Works offline — `ti-wifi-off`
2. No ads for kids — `ti-ad-off`
3. Parent dashboard — `ti-users`
4. NCERT aligned — `ti-school`

---

### WORKSHEET LIBRARY (`#worksheets`)
Cream background. Data-driven **4-level nested accordion**: Subject → Class → Topic → Sheets.

#### JS data structure (`WORKSHEETS` array)
Defined in a `<script>` block at the bottom of `index.html`.

```js
const WORKSHEETS = [
  {
    subject: 'Math', subjectHi: 'गणित', color: '#FF6B35', icon: '🔢',
    classes: [
      { cls: 'Class 1', topics: [
          { topic: 'Counting', topicHi: 'गिनती', sheets: [
              { name: 'Counting 1–10', file: null },   // null = Coming soon
              { name: 'Counting 11–20', file: null },
          ]},
          // … more topics
      ]},
      // Class 2–5
    ]
  },
  // Science, GK, English
];
```

Subjects and their topics:

| Subject | Color | Topics (same across Class 1–5, progressively deeper) |
|---|---|---|
| Math | #FF6B35 | Counting, Addition, Subtraction, Multiplication, Division, Shapes, Fractions / Decimals |
| Science | #16A34A | Plants, Animals, Water, Weather, Earth, Space |
| GK | #7C3AED | Countries & Capitals, Famous Animals, India, Inventions, Festivals |
| English | #2D9CDB | Alphabet, Spelling, Vocabulary, Grammar, Sentences, Opposites, Tenses |

All `file` values are currently `null` (every sheet row shows "Coming soon"). Set `file` to a path string to activate a download link.

#### Accordion rendering (`renderWorksheetLibrary()`)
Called on `DOMContentLoaded`. Writes into `<div id="worksheet-library">`.

Generates three levels of `.worksheet-card` + `.worksheet-toggle` + `.accordion-panel`:
- **Level 1 (Subject):** `.subject-toggle` — subject icon, name, Hindi subtitle, coloured left border, "Class 1–5" meta pill
- **Level 2 (Class):** `.class-toggle` — "Class 1" … "Class 5", indented 28 px
- **Level 3 (Topic):** `.topic-toggle` — topic name + Hindi subtitle, indented 36 px, cream tinted background
- **Level 4 (Sheets):** `.sheet-row` — either a `.sheet-link` download button (when `file != null`) or a muted `.sheet-soon` + `.soon-badge` "Soon" tag (when `file == null`)

#### Accordion JS (click delegation)
Single `document.addEventListener('click', …)` handler. Uses `event.target.closest('.worksheet-toggle')`.

**Opening a panel:**
```js
const expandHeight = panel.scrollHeight;
panel.style.maxHeight = expandHeight + 'px';
// Walk up ancestor .accordion-panel elements and grow their maxHeight
let ancestor = btn.closest('.accordion-panel');
while (ancestor) {
  if (ancestor.classList.contains('open'))
    ancestor.style.maxHeight = (parseFloat(ancestor.style.maxHeight) || 0) + expandHeight + 'px';
  ancestor = ancestor.parentElement?.closest('.accordion-panel');
}
```

**Closing a panel:**  
Records current `maxHeight`, clears it (`''`), then shrinks ancestor panels by the same amount.

This ensures parent panels always have enough height to show fully-expanded children — the key fix for nested accordions where `scrollHeight` is measured once at open time.

CSS: `.accordion-panel { max-height: 0; overflow: hidden; transition: max-height 0.25s ease; }`

#### Nudge card (`.nudge`)
Dark strip below the accordion: "Want more practice?" + "Get GyanQuest →" CTA. Links to `#` (needs Play Store URL). The GyanQuest app icon (192 px) appears as a small rounded thumbnail on the left.

---

### KINDERGARTEN (`#kindergarten`)
White background (`#fff`). **2-level accordion**: Category → Sheets.

#### JS data structure (`KINDERGARTEN_ACTIVITYSHEETS` array)
```js
const KINDERGARTEN_ACTIVITYSHEETS = [
  { category: 'Tracing & Pre-writing', categoryHi: 'लेखन से पहले', icon: '✏️',
    sheets: [ { name: 'Trace straight lines', file: null }, … ] },
  // Colouring, Shapes & Patterns, Numbers 1–10, Letters A–Z, Animals & Things
];
```

Six categories, 3 placeholder sheets each (all `file: null`).

#### Rendering (`renderKindergartenLibrary()`)
Writes into `<div id="kindergarten-library">`. Uses same `.worksheet-card` / `.subject-toggle` / `.accordion-panel` / `.sheet-row` classes as the main library. KG categories use `--accent: var(--gold)` for the left border.

At the bottom of the section: `<p class="kindergarten-note">More activity sheets coming soon.</p>`

---

### ABOUT (`#about`)
Centred text, cream background.
- Orange uppercase label "About Omnispark"
- Body paragraph about Omnispark's mission
- Italic coda: "GyanQuest is our first app. More are coming."

---

### FOOTER (`.site-footer`)
Dark background. Two rows:
1. Brand (logo + "Made with care in India") + links: Privacy Policy → `privacy.html`, Terms → `#`, Contact → `#`
2. "© 2025 Omnispark. All rights reserved."

---

## Responsive breakpoints

| Breakpoint | Changes |
|---|---|
| `max-width: 768px` | Nav links hidden, hamburger shown; App grid → 1 col; nudge stacks vertically; footer stacks |
| `max-width: 480px` | Reduced padding; hero buttons stack full-width |

The worksheet accordion is a single column at all widths (no grid — each subject card is full-width).

---

## CSS utility classes (worksheet-specific)

| Class | Purpose |
|---|---|
| `.worksheet-card` | White card with border-radius 16px, used at subject, class, and topic levels |
| `.worksheet-toggle` | Base button style for all accordion headers |
| `.subject-toggle` | Level-1 toggle: left border uses `--accent` CSS var, larger padding |
| `.class-toggle` | Level-2 toggle: 28 px left indent |
| `.topic-toggle` | Level-3 toggle: 36 px left indent, cream tint |
| `.accordion-panel` | Hidden container; gets `open` class + inline `maxHeight` style when expanded |
| `.sheet-row` | Leaf row: icon + name on left, action on right |
| `.sheet-link` | Orange outline pill — rendered when `file != null` |
| `.sheet-soon` | Muted grey pill — rendered when `file == null` |
| `.soon-badge` | "SOON" uppercase tag inside `.sheet-soon` |
| `.pill` / `.pill-soft` | Subject tags in app card; `.pill-soft` = muted grey for coming-soon subjects |
| `.soon-tag` | Inline muted badge used in `.app-olympiad-note` |
| `.nudge` | Dark CTA strip below worksheet library |
| `.kindergarten-note` | Muted centred note at KG section foot |

---

## What's NOT done yet

- **Play Store URL** — GyanQuest is not yet published; all "Get on Google Play" / "Get GyanQuest" links point to `#`
- **Real worksheet PDFs** — all `file` values in `WORKSHEETS` and `KINDERGARTEN_ACTIVITYSHEETS` are `null`; no PDFs exist in `assets/worksheets/`
- **Placeholder pages** — `about.html`, `contact.html`, `terms.html` are bare-bones shells without real content or shared styling
- **`gyanquest.html`** — dedicated app landing page (placeholder / not built)
- **`assets/css/styles.css`** and **`assets/js/main.js`** — referenced by placeholder pages but not yet created
- **Footer links** — Terms and Contact still point to `#`
- **Favicon** — not yet added
- **OG / Twitter meta tags** — not yet added
- **Analytics** — not integrated
- **`node_modules/`, `package.json`, `package-lock.json`** — Playwright dev artifacts in repo root; should be gitignored

---

## Brand voice & positioning

- Indie / small studio, not corporate
- "Made in India" — proud, local identity
- Target audience: Indian parents with children in Classes 1–5; professional, trustworthy tone
- Value props: free, offline-first, no ads, NCERT-aligned, bilingual
- GyanQuest is the first product; "More are coming" hints at a multi-app future under Omnispark
