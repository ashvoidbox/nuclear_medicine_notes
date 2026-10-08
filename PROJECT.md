# RadioBiology Study Notes — Project Brief

> **Read this file at the start of every session involving this project.**
> It is the authoritative record of what exists, what decisions have been made, and what comes next.

---

## Project Overview

A self-contained, single-file HTML study reference for a **RadioBiology** course unit on **Interaction of Radiation with Matter**. The target device is an **iPad** (primary) and desktop browser (secondary). No build system, no frameworks, no external assets — everything is embedded in one HTML file.

**File:** `Interaction_of_Radiation_with_Matter_Notes.html`
**Location:** `/Volumes/G-DRIVE ArmorATD/IBM/NuclearMedicine/`
**Course:** RadioBiology  
**Topic:** Interaction of Radiation with Matter

---

## Current File State

### Structure

The file is a single self-contained HTML document with:
- Inline CSS (`<style>` block)
- Inline JavaScript (`<script>` block at bottom)
- All images embedded as base64 data URIs (no external files)

### Layout

```
<header>                        ← sticky, fixed-height (min-height: 52px, single row)
  <h1> title                    ← flex: 1
  <div.header-right>            ← single-row right panel
    <div.header-bot-row>        ← Search input | ← Prev | count | Next →
<div.layout>                    ← max-width: 1200px, flex row, height: calc(100vh - 52px), overflow: hidden
  <div.sidebar>                 ← 210px, flex column, height: 100%, overflow: hidden
    <div.tools-panel>           ← fixed, flex-shrink: 0 — Study Tools title + Quiz btn + Avg badge + Highlights btn
    <div.toc-heading>           ← fixed, flex-shrink: 0 — "Contents" label
    <nav#toc>                   ← flex: 1 1 0, min-height: 0, overflow-y: auto — scrollable section links
  <main>                        ← flex: 1, overflow-y: auto, height: 100% — all content scrolls here
    <div.no-results>            ← search "no match" banner
    <div.section id="s1–s16">  ← 16 content sections
```

### 16 Content Sections

| ID  | Title |
|-----|-------|
| s1  | Introduction |
| s2  | What Interacts |
| s3  | Passage Through Matter |
| s4  | Range of Radiation |
| s5  | Gamma and X-rays Interact with Matter |
| s6  | Photoelectric Effect |
| s7  | Coherent Scatter |
| s8  | Compton Scatter |
| s9  | Pair Production |
| s10 | Photodisintegration |
| s11 | Probability of Each Type of Interaction |
| s12 | Particles Interact with Matter |
| s13 | Neutrons Interact with Matter |
| s14 | Relative Biological Effectiveness (RBE) |
| s15 | Radiation Weighting Factors (Wr) |
| s16 | Glossary of Terms |

### Section content format

Each section uses:
```html
<div class="section" id="sN">
  <div class="section-header">
    <div class="section-num">N</div>
    <h2>Section Title</h2>
  </div>
  <div class="section-body">
    <!-- content: p, ul, ol, h3, img, .formula-box, .callout, .callout.warning, table -->
  </div>
</div>
```

#### Reusable content components (CSS classes)
- `.formula-box` — yellow-highlight monospace block for equations
- `.callout` — blue left-border info callout
- `.callout.warning` — orange left-border warning callout
- `.section-body h3` — dark subheading inside a section body

---

## Features Implemented

### 1. Sticky Header (fixed height)
- Always `min-height: 78px` — never reflows regardless of search state.
- Title left, right-panel with 2 stacked rows.

### 2. Search Bar
- Text input in `.header-bot-row`.
- 260ms debounce, case-insensitive regex search across all visible text in `<main>`.
- Highlights matches with `<mark class="hl">` (yellow), current match in orange (`mark.hl.current`).
- `← Prev` / `Next →` buttons always visible; start `.inactive` (dimmed, `pointer-events:none`).
- Buttons activate (`setNavActive(true)`) when matches are found; deactivate on clear/empty/no-results.
- Count display `— / —` → `N / total` when active, `0 found` on no results.
- `✕` clear button appears in input when text is present.
- `setNavActive()` is the single function that controls button state + count reset.

### 3. Quiz System
- **Quiz button** in `.page-bar` (above s1, page-level bar).
- **Avg badge** (`#quizAvg`) sits to the right of the quiz button in the page bar.
- Quiz average persists in `localStorage` under key `radQuizScores` (array of % integers, capped at 20).
- `loadAvg()` called on page load to restore display. `saveScore(pct)` called after each submission.
- Modal overlay (`div.quiz-overlay`) — opens on Quiz button tap, closes on ✕ or backdrop tap.
- **Question bank: 41 questions** covering all 15 course sections.
- Each session: 10 random questions drawn from bank (`shuffle(QUIZ_BANK).slice(0,10)`).
- Answer choices shuffled per render (correct answer index tracked via `data-correct` attribute).
- On submit: validates all answered, then grades — correct = green, wrong = red, correct answer revealed.
- Results screen: `X / 10` large score, percentage, encouragement message, full question review.
- "Try Another Quiz" re-randomises and re-renders.
- Inline per-question feedback: `.quiz-feedback.ok` / `.quiz-feedback.bad`.

### 4. Glossary (Section 16)
- 30 term cards in a responsive CSS grid (`minmax(280px, 1fr)`).
- `<dl class="glossary-grid">` with `<div class="glossary-card"><dt>…<dd>…</div>` pattern.
- TOC entry #16 present.

### 5. TOC Sidebar
- `IntersectionObserver` highlights active section as user scrolls.
- TOC links use `data-target` + JS scroll (not anchor `href`) so scroll position offsets for the sticky header.
- `top: 78px` to match header height.

### 6. Responsive / iPad
- Sidebar hides on `max-width: 600px` (phones).
- Sidebar narrows at `601–900px` (iPad portrait).
- Header wraps at `max-width: 600px` — title goes full-width, right panel becomes horizontal.

---

## Design System

| Token | Value |
|-------|-------|
| Primary blue | `#1a56a6` |
| Page background | `#f0f2f5` |
| Card background | `#ffffff` |
| Surface | `#f7f8fa` |
| Border | `#e5e7eb` |
| Text | `#1f2328` |
| Muted | `#57606a` |
| Highlight yellow | `#fef08a` |
| Highlight orange | `#f97316` |
| Success green | `#16a34a` |
| Error red | `#dc2626` |
| Formula yellow bg | `#fefce8` |
| Font | `-apple-system, 'Segoe UI', system-ui, sans-serif` |
| Base font size | `15px`, `line-height: 1.7` |

---

## Key Conventions

- **No external dependencies.** All CSS, JS, and images are inline.
- **Images** are base64-encoded JPEGs/PNGs embedded as `src="data:image/...;base64,..."`.
- **No `display:none` toggling on fixed-layout elements.** Use `opacity`/`pointer-events` instead (e.g., `.inactive` class on nav buttons).
- **`localStorage` key namespace:** `radQuiz*` (e.g., `radQuizScores`).
- **Section IDs:** `s1`–`s16`. Next section added would be `s17`, TOC entry added to match.
- **Quiz bank:** `var QUIZ_BANK` array at top of quiz section in `<script>`. Each entry: `{ section: "sN", q: "…", a: <correct_index_in_choices>, choices: ["A","B","C","D"] }`. The `a` index refers to the **original** `choices` array (before shuffling). `section` maps to a section ID (s2–s15) for per-section miss-rate tracking.
- **HTML structure:** All section body content is indented multi-line (not single-line dense markup). Use `<h3>` for subheadings inside section bodies, not `<u><strong>`.

---

## Decisions Made (with rationale)

| Decision | Rationale |
|----------|-----------|
| Single HTML file | iPad-friendly, no server required, easily shared/airdropped |
| No frameworks | Zero dependencies, loads instantly, no build step |
| base64 images | Self-contained — file works offline and when moved |
| Fixed header height, always-visible nav buttons | Prevents layout reflow on iPad which feels "sloppy" |
| `localStorage` for quiz scores | Persistence without a server; survives page reloads and app closure |
| 20-attempt rolling window for average | Reflects recent performance rather than all-time history |
| Shuffle choices on render | Prevents pattern memorisation |
| `data-correct` attribute on question divs | Survives innerHTML replacement during grading |
| `h3` for subheadings (not `<u><strong>`) | Semantic markup, better accessibility, cleaner CSS |

---

## Planned / Possible Future Work

- [ ] Add more course units as new sections (e.g., Attenuation of Radiation, Cell Biology, Radiotherapy)
- [ ] Expand quiz bank as new sections are added
- [ ] Add a "bookmarks" feature (localStorage) to mark sections for review
- [ ] Add a dark mode toggle
- [ ] Consider splitting into multiple HTML files if the single file grows too large (>5 MB due to base64 images)
- [ ] Add a progress tracker (e.g., "sections read" stored in localStorage)
- [ ] Add print/PDF stylesheet for offline paper notes

---

## Session Log

| Date | Changes |
|------|---------|
| Oct 2024 | Initial file created by Scott Clinefelter |
| Session 1 | Reformatted all 15 section bodies from dense single-line HTML to properly indented, semantic markup; replaced `<u><strong>` pseudo-headings with `<h3>`; removed redundant title/welcome text from s1; fixed `<p><img>` wrapping |
| Session 2 | Removed author byline from header; added search bar with highlight/arrow navigation; added Section 16 Glossary (30 terms) with TOC entry |
| Session 3 | Added ✎ Quiz button + modal; 41-question bank; random 10-question draw; shuffled choices; graded results with per-question feedback and review screen |
| Session 4 | Refactored header to fixed 2-row right panel (no reflow); nav arrows always visible, activate/deactivate via `.inactive`; quiz average badge (localStorage, rolling 20); quiz button aligned to search row |
| Session 5 | Created PROJECT.md + Bob memory note for project persistence |
| Session 6 | Added `section` property to all 41 QUIZ_BANK questions; added per-section stat tracking in `gradeQuiz()` → localStorage key `radQuizSectionStats`; removed `.header-top-row` from header, collapsed header to single row (52px); moved quiz button + avg badge to new `.page-bar` above s1; added `🔍 Study Highlights` toggle button (right side of page bar); implemented `applyHighlights()` — yellow (`.study-warn`) for 50–85% correct, red (`.study-crit`) for <50% correct, on sections s2–s15; updated TOC/nav `top` from 78px → 52px |
| Session 7 | Added `⌂ Menu` home button to sticky header (far left, links to `index.html`); removed `RadioBiology Study Notes · ashvoidbox` subtitle from `index.html` header; moved all project files into `NuclearMedicine/` subfolder; updated `AGENTS.md` and `PROJECT.md` paths and feature summary to reflect current state |
| Session 8 | Sidebar refactor: wrapped `nav#toc` + new `.tools-panel` in a `.sidebar` flex column; sidebar is now fixed-height (`height: calc(100vh - 52px)`, `overflow: hidden`) — never scrolls; TOC links use `display:flex` with `.toc-num` (`min-width:22px`) + `.toc-label` (flex:1) so all section titles align uniformly; moved Quiz button, Avg badge, and Study Highlights toggle into `.tools-panel`; removed old `.page-bar` from `<main>`; scroll model changed to `<main>` as scroll container (`overflow-y:auto`) with `html/body` set to `overflow:hidden`; all JS scroll targets updated to `mainEl2`; IntersectionObserver `root` set to `mainEl2`; removed `touchend` listener on TOC links (caused double-fire on iPad); final sidebar order: `.tools-panel` (fixed) → `.toc-heading` (fixed) → `nav#toc` (scrollable) |
