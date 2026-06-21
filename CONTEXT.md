# Omnispark Website — Full Context

## Project overview

**omnispark.in** is the marketing website for Omnispark, a small independent EdTech studio building digital learning tools for Indian kids (Classes 1–5). The site promotes two things:
1. **GyanQuest** — a free Android app (offline-first, NCERT-aligned, no ads, parent dashboard)
2. **Free printable worksheets** — downloadable PDFs organized by subject and topic

Domain: omnispark.in  
Stack: Pure HTML/CSS/JS (no framework, no build step). Static site.

---

## File structure

```
index.html          ← Main marketing page (fully built, polished)
about.html          ← Placeholder (bare bones, not yet styled)
gyanquest.html      ← Placeholder (bare bones, not yet styled)
vidya.html          ← Redirect shell → gyanquest.html (meta refresh)
contact.html        ← Placeholder (bare bones, not yet styled)
privacy.html        ← Placeholder (bare bones, not yet styled)
terms.html          ← Placeholder (bare bones, not yet styled)

assets/
  worksheets/
    math/
      addition/           (3 PDFs: single-digit, double-digit, with-carry-over)
      subtraction/        (2 PDFs: basic-subtraction, borrowing-method)
      multiplication/     (2 PDFs: tables-1-5, tables-6-10)
      counting/           (2 PDFs: 1-to-20, 1-to-100)
    science/
      plants/             (2 PDFs: parts-of-a-plant, types-of-plants)
      animals/            (2 PDFs: domestic-wild-animals, animal-habitats)
      water/              (2 PDFs: uses-of-water, water-cycle)
      weather/            (2 PDFs: seasons, types-of-weather)
    gk/
      india/              (2 PDFs: states-capitals, national-symbols)
      countries/          (2 PDFs: countries-capitals, flags-continents)
      inventions/         (2 PDFs: famous-inventions, indian-scientists)
      festivals/          (2 PDFs: festivals-of-india, festivals-around-the-world)
  css/
    styles.css            ← Referenced by placeholder pages (not yet created)
  js/
    main.js               ← Referenced by placeholder pages (not yet created)
```

PDF naming convention: `{topic}-{worksheet-name}.pdf`  
Example: `assets/worksheets/math/addition/addition-single-digit.pdf`

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
- Weights used: Nunito 400/500/600/700/800; Playfair Display 600/700 + italic

### Icons
- Tabler Icons webfont via CDN (`@tabler/icons-webfont`)
- Used as `<i class="ti ti-{icon-name}"></i>`
- Icons in use: ti-sparkles, ti-book-2, ti-brand-google-play, ti-wifi-off, ti-ad-off, ti-users, ti-school, ti-chevron-down, ti-download

---

### NAV (`#main-nav`)
- Fixed/sticky, `z-index: 300`
- Transparent on hero; becomes **frosted glass** (`rgba(255,248,240,0.9)` + `backdrop-filter: blur(14px)`) after 40px scroll
- Class `.scrolled` is toggled by JS on the `scroll` event
- Logo: orange rounded square with "O" + "Omnispark" in Playfair Display
- Desktop links: Worksheets → `#worksheets`, About → `#about`
- CTA button: "Get GyanQuest" (currently links to `#`, needs real Play Store URL)
- Mobile: nav links and CTA are hidden; a **hamburger button** shows instead
- Hamburger animates to X when open (CSS transforms on 3 `<span>` elements)
- Mobile drawer (`#mobile-drawer`) slides in below nav with same links

**JS functions:**
- `toggleNav()` — called by hamburger `onclick`; toggles `.open` on drawer and hamburger
- `closeNav()` — called by drawer links `onclick`; closes drawer

---

### HERO (`#hero`)
- Full viewport height, dark background (`var(--dark)`)
- Decorative layers (all `pointer-events: none`):
  - `.hero-grid-bg` — subtle white grid lines via `background-image` linear-gradients
  - `::before` pseudo-element — two radial orange/gold glows
- Badge: pill with sparkles icon + "Indie EdTech · Made in India"
- `h1`: "Your child's learning companion for Classes 1–5" (italic "Classes 1–5" in gold)
- Subtext: describes free worksheets + free app, NCERT-aligned, bilingual, offline-first
- Two CTA buttons:
  - `.btn-fill` "Download Worksheets" → `#worksheets` (smooth scroll)
  - `.btn-ghost` "Get the App" → `#` (needs Play Store URL)
- Scroll hint: animated mouse icon + "SCROLL" label at bottom center

---

### APP SHOWCASE (`#app`)
White background. Two-column grid (`.app-grid`): left = app card, right = feature list.

**App card (`.app-card`):**
- Orange icon, type label "Free EdTech App", name "GyanQuest" in Playfair Display
- Description + subject pills: Math, Science, GK, English, Olympiad
- Two buttons: "Get on Google Play" (dark filled, Google Play icon) + "Learn more" (dark outline)
- Both link to `#` — need real URLs

**Feature list (`.feat-list`):**
Four rows with icon + heading + description:
1. Works offline — ti-wifi-off — "No internet needed once installed."
2. No ads for kids — ti-ad-off — "Child-safe, distraction-free environment."
3. Parent dashboard — ti-users — "Track progress per subject & topic."
4. NCERT aligned — ti-school — "Covers Classes 1–5 curriculum."

---

### WORKSHEET LIBRARY (`#worksheets`)
Cream background. Three subject cards in a responsive grid (3 cols → 2 cols at 900px → 1 col at 768px).

**Subject cards (`.subj-card`):**
Each card has a colored dot + subject name header, then an accordion topic list.

| Subject | Dot color | Topics |
|---|---|---|
| Mathematics | #FF6B35 (orange) | Addition, Subtraction, Multiplication, Counting |
| Science | #16A34A (green) | Plants, Animals, Water, Weather |
| General Knowledge | #7C3AED (purple) | India, Countries, Inventions, Festivals |

**Accordion behavior (JS `toggleTopic(btn)`):**
- One topic open at a time **per card** (others in same card collapse)
- Active topic button gets class `.open` → orange text + chevron rotates 180°
- Sheet list gets class `.open` → `display: block`
- Clicking an open topic closes it (toggle behavior)

**Sheet rows (`.sheet-row`):**
Each has the worksheet name + a download link (`<a download>`) pointing to the PDF.

**Nudge card (`.nudge`):**
Dark background strip below the grid. "Want more practice?" + "Get GyanQuest →" CTA button.

---

### ABOUT (`#about`)
- Centered text section, cream background
- Orange uppercase label "About Omnispark"
- Body: "Omnispark is a small independent studio building digital learning tools for kids and students across India. We believe good education software should be safe, joyful, and work for every family — not just the ones with fast internet."
- Coda (italic, muted): "GyanQuest is our first app. More are coming."

---

### FOOTER (`.site-footer`)
Dark background. Two rows:
1. Brand (logo + "Made with care in India") + link list: Privacy Policy, Terms, Contact
2. Copyright line: "© 2025 Omnispark. All rights reserved."

Footer links currently point to `#` — should point to `privacy.html`, `terms.html`, `contact.html`.

---

## Placeholder pages (about.html, vidya.html, contact.html, privacy.html, terms.html)

All five share the same bare-bones structure:
```html
<header><h1>Page Title</h1><nav>…6 links…</nav></header>
<main><h2>…</h2><p>One placeholder sentence.</p></main>
<footer><p>© 2026 OmniSpark.</p></footer>
```
- Link to `assets/css/styles.css` and `assets/js/main.js` — **neither file exists yet**
- These pages need to be built out with real content and matching styling

---

## Responsive breakpoints

| Breakpoint | Changes |
|---|---|
| `max-width: 900px` | Worksheet grid: 3 col → 2 col |
| `max-width: 768px` | Nav links hidden, hamburger shown; App grid 2-col → 1-col; worksheet grid → 1-col; nudge stacks vertically; footer stacks |
| `max-width: 480px` | Reduced padding on `.sec` and hero; hero buttons stack vertically full-width |

---

## What's NOT done yet

- Play Store URL for GyanQuest (both nav CTA and app section buttons)
- `assets/css/styles.css` — shared stylesheet for placeholder pages
- `assets/js/main.js` — shared JS for placeholder pages
- Content for vidya.html (dedicated GyanQuest app page)
- Content for about.html, contact.html, privacy.html, terms.html
- Footer links need to point to real pages instead of `#`
- Real worksheet PDFs (current files are placeholders — 0 bytes)
- Favicon
- OG/Twitter meta tags for social sharing
- Any analytics integration

---

## Brand voice & positioning

- Indie / small studio, not corporate
- "Made in India" — proud, local identity
- Target audience: Indian families with children in Classes 1–5
- Value props: free, offline-first, no ads, NCERT-aligned, bilingual
- Tone: warm, accessible, trustworthy
- GyanQuest is the first product; "More are coming" hints at a multi-app future under Omnispark
