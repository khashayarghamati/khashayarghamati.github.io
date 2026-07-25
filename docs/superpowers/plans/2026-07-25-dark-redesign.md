# Dark-First Redesign (Midnight Indigo) Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Re-theme ghamati.com (all pages except `/music/`) to the approved dark-first Midnight Indigo + Space Grotesk design by rewriting the shared design-system tokens and fixing per-page hardcoded light styles.

**Architecture:** One shared stylesheet (`css/material-design.css`) drives 13 pages via `md-*` classes. Rewrite its token layer dark-by-default, convert the existing `prefers-color-scheme: dark` block into a *light* fallback, flatten tonal-gradient elevation into flat surfaces + borders, then audit each page for hardcoded light-mode colors and recolor. `404.html` is standalone (own CSS variables). `/music/` must remain byte-identical.

**Tech Stack:** Static HTML/CSS, Google Fonts (Space Grotesk + Inter), GitHub Pages. No build step. Verification via grep + local `python3 -m http.server` + Chrome (claude-in-chrome).

## Global Constraints

- `/music/**` must not change: `git status --short music/` must stay empty throughout.
- No circular shapes: never introduce `border-radius: 50%`, `border-radius: 50px`, or `var(--md-shape-full)` usages.
- Spacing stays on the 4/8dp grid; do not change existing spacing values.
- JS behavior unchanged (scrollspy, FAB, reveal, dialogs).
- Content/copy, section order, SEO/meta/JSON-LD unchanged.
- Canonical palette (from spec): bg `#0C1330`, cards `#141D45`, section-alt `#0F1738`, borders `#253266`/strong `#33407A`, text `#EEF1FF`, secondary text `#9AA6D8`, primary `#7C93FF` (on-primary `#0C1330`), gold `#C9A45C`, hero gradient `linear-gradient(150deg,#0C1330 0%,#111A3E 55%,#182456 100%)`, footer `#080D22`, published `#4ADE80` on `#123C2B`, review `#FBBF24` on `#3E2E10`.
- Light fallback palette: bg `#FAFAF7`, surface-variant `#FFFFFF`, section-alt `#F1F2F7`, text `#101430`, secondary `#5A6384`, primary `#3D53C6`, gold `#B45309`, borders `#E3E6F2`/strong `#B9C0DA`.

---

### Task 1: Token layer — dark default + light fallback + typography

**Files:**
- Modify: `css/material-design.css` (sections 2 Color Tokens, 7 Typography, 35 Dark Mode)

**Interfaces:**
- Produces: CSS custom properties consumed by every page — same names as today plus one new token `--md-surface-dim` (section-alt background). All later tasks rely on: `--md-primary:#7C93FF`, `--md-surface:#0C1330`, `--md-surface-variant:#141D45`, `--md-surface-dim:#0F1738`, `--md-on-surface:#EEF1FF`, `--md-on-surface-variant:#9AA6D8`, `--md-outline:#33407A`, `--md-outline-variant:#253266`, `--md-tertiary:#C9A45C`, `--md-font-display:'Space Grotesk'`.

- [ ] **Step 1: Replace the `:root` color token block (section 2)** with:

```css
:root {
  /* Primary — periwinkle on midnight indigo */
  --md-primary: #7c93ff;
  --md-on-primary: #0c1330;
  --md-primary-container: #253266;
  --md-on-primary-container: #c3cdf5;

  /* Secondary */
  --md-secondary: #8a94c4;
  --md-on-secondary: #0c1330;
  --md-secondary-container: #1b2450;
  --md-on-secondary-container: #c3cdf5;

  /* Tertiary — warm gold accent */
  --md-tertiary: #c9a45c;
  --md-on-tertiary: #241a05;
  --md-tertiary-container: #3a2f14;
  --md-on-tertiary-container: #e8d5a8;

  /* Error */
  --md-error: #fca5a5;
  --md-on-error: #450a0a;
  --md-error-container: #7f1d1d;
  --md-on-error-container: #fee2e2;

  /* Surface & Background — Midnight Indigo */
  --md-surface: #0c1330;
  --md-surface-dim: #0f1738;
  --md-surface-bright: #1b2450;
  --md-surface-variant: #141d45;
  --md-on-surface: #eef1ff;
  --md-on-surface-variant: #9aa6d8;

  /* Outline */
  --md-outline: #33407a;
  --md-outline-variant: #253266;

  /* Inverse */
  --md-inverse-surface: #eef1ff;
  --md-inverse-on-surface: #101430;
  --md-inverse-primary: #3d53c6;

  --md-surface-tint: #7c93ff;
  --md-scrim: #000000;
  --md-shadow: #000000;
}
```

- [ ] **Step 2: Typography tokens + headline styling.** In section 7 replace the font-family tokens and tighten headline classes:

```css
:root {
  --md-font-display: 'Space Grotesk', 'Inter', sans-serif;
  --md-font-headline: 'Space Grotesk', 'Inter', sans-serif;
  --md-font-title: 'Inter', sans-serif;
  --md-font-body: 'Inter', sans-serif;
  --md-font-label: 'Inter', sans-serif;
}
```

And on `.md-display-large/medium/small` and `.md-headline-large/medium/small`, set `font-weight: 700;` and `letter-spacing: -0.02em;` (replace each class's current `font-weight`/`letter-spacing` lines; keep sizes/line-heights).

- [ ] **Step 3: Replace the `@media (prefers-color-scheme: dark)` block (section 35)** with a *light* fallback:

```css
@media (prefers-color-scheme: light) {
  :root {
    --md-primary: #3d53c6;
    --md-on-primary: #ffffff;
    --md-primary-container: #e0e6ff;
    --md-on-primary-container: #1b2a80;

    --md-secondary: #5a6384;
    --md-on-secondary: #ffffff;
    --md-secondary-container: #e6e9f5;
    --md-on-secondary-container: #232a4d;

    --md-tertiary: #b45309;
    --md-on-tertiary: #ffffff;
    --md-tertiary-container: #fef3c7;
    --md-on-tertiary-container: #78350f;

    --md-error: #dc2626;
    --md-on-error: #ffffff;
    --md-error-container: #fee2e2;
    --md-on-error-container: #7f1d1d;

    --md-surface: #fafaf7;
    --md-surface-dim: #f1f2f7;
    --md-surface-bright: #ffffff;
    --md-surface-variant: #ffffff;
    --md-on-surface: #101430;
    --md-on-surface-variant: #5a6384;

    --md-outline: #b9c0da;
    --md-outline-variant: #e3e6f2;

    --md-inverse-surface: #101430;
    --md-inverse-on-surface: #eef1ff;
    --md-inverse-primary: #7c93ff;

    --md-surface-tint: #3d53c6;
  }

  .md-top-app-bar {
    background: rgba(250, 250, 247, 0.85);
  }

  .md-publication-status--published { background-color: #dcf5e3; color: #14532d; }
  .md-publication-status--review { background-color: #fdf0d2; color: #92400e; }
}
```

Delete every other rule currently inside the old dark-mode block (dark elevation overrides, dark hero, dark cards, dark buttons, dark scrollbar, dark nav bar, dark dialog, dark data table, dark author card, dark stat badge, dark publication statuses) — they become dead once dark is the default.

- [ ] **Step 4: Verify** `grep -c "prefers-color-scheme: dark" css/material-design.css` → 0; `grep -c "1e40af\|dbeafe\|f1f5f9" css/material-design.css` → 0.

- [ ] **Step 5: Commit** `git add css/material-design.css && git commit -m "redesign: dark-first Midnight Indigo token layer + Space Grotesk"`

---

### Task 2: Shared components — flat dark surfaces, hero, nav, status, figures

**Files:**
- Modify: `css/material-design.css` (sections 5, 10, 11, 14, 19, 20, 21, 22, 24, 26, 28, 29)

**Interfaces:**
- Consumes: tokens from Task 1.
- Produces: component classes with identical names/markup contracts (no HTML edits needed on var-driven pages).

- [ ] **Step 1: Flatten tonal elevation.** Replace every `background: linear-gradient(0deg, rgba(26, 95, 180, X), rgba(26, 95, 180, X)), var(--md-surface);` in `.md-elevation-1..5`, `.md-card-elevated`, `.md-author-card`, `.md-stat-badge`, `.md-navigation-bar`, `.md-dialog` with `background-color: var(--md-surface-variant);` (dialog uses `var(--md-surface-bright)`). Add `border: 1px solid var(--md-outline-variant);` to `.md-card-elevated`, `.md-author-card`, `.md-stat-badge`, `.md-method-card`, `.md-dialog`.

- [ ] **Step 2: Card hover = border brighten + existing lift.** In `.md-card-elevated:hover`, `.md-card-filled:hover`, `.md-card-outlined:hover`, `.md-method-card:hover` add `border-color: var(--md-outline);` (keep existing shadow + `translateY(-2px)`).

- [ ] **Step 3: Top app bar dark glass.** `.md-top-app-bar { background: rgba(12, 19, 48, 0.85); }` (keep blur lines). `.md-top-app-bar.md-scrolled { box-shadow: 0 1px 3px rgba(0,0,0,0.4); }`.

- [ ] **Step 4: Hero.** Replace `.md-hero` background with `linear-gradient(150deg, #0c1330 0%, #111a3e 55%, #182456 100%)`. Replace `.md-hero .md-btn-filled { background-color:#ffffff; color:#1e40af; }` with `background-color: var(--md-primary); color: var(--md-on-primary);` and its `:hover` white-gradient with `filter: brightness(1.1); box-shadow: var(--md-elevation-2);`.

- [ ] **Step 5: State layers.** Replace remaining hardcoded state hover colors: every `rgba(26, 95, 180, var(--md-state-*))` → `rgba(124, 147, 255, var(--md-state-*))`; every `rgba(26, 28, 30, var(--md-state-*))` → `rgba(238, 241, 255, var(--md-state-*))`; every `rgba(17, 28, 43, var(--md-state-*))` and `rgba(0, 28, 58, ...)` and `rgba(0, 48, 96, ...)` → `rgba(12, 19, 48, var(--md-state-*))`; buttons' white-gradient hovers on filled → `filter: brightness(1.1);`.

- [ ] **Step 6: Publication status (dark).** `--published`: `background-color:#123c2b; color:#4ade80;` · `--review`: `background-color:#3e2e10; color:#fbbf24;` · `--accepted`: `background-color: var(--md-primary-container); color: var(--md-on-primary-container);` (unchanged, tokens now dark).

- [ ] **Step 7: Figures stay light.** `.md-figure { background-color: #ffffff; border: 1px solid #e3e6f2; }` and `.md-figure figcaption, .md-figure__caption { color: #5a6072; }` with `strong { color: #101430; }` — diagrams keep a white frame on the dark page (spec §3).

- [ ] **Step 8: Data tables.** `.md-data-table { background-color: var(--md-surface-variant); }` (replace white/gradient), header/body colors already var-driven; row hover `rgba(238,241,255,0.06)`.

- [ ] **Step 9: Disabled buttons.** Replace `rgba(26, 28, 30, 0.12)` / `rgba(26, 28, 30, 0.38)` in `:disabled` rules with `rgba(238, 241, 255, 0.12)` / `rgba(238, 241, 255, 0.38)`.

- [ ] **Step 10: Verify** `grep -c "rgba(26, 95, 180\|rgba(26, 28, 30\|rgba(17, 28, 43\|c8e6c9\|fff3e0\|#0f172a" css/material-design.css` → 0. Serve locally, load `/pllm/` in Chrome: dark hero, dark cards, readable text, white figure frames.

- [ ] **Step 11: Commit** `git add css/material-design.css && git commit -m "redesign: dark component styles — flat surfaces, dark glass nav, status pills"`

---

### Task 3: All pages — Space Grotesk font link + cache-busted CSS link

**Files:**
- Modify: `index.html`, `CAPA-AI/index.html`, `brlbam/index.html`, `graphrag-bn-recsys/index.html`, `har-agent-tmm/index.html`, `har-agent/index.html`, `icsr-2026/index.html`, `isvsicl/index.html`, `pllm/index.html`, `post-training-vs-continual-learning/index.html`, `rlbam/index.html`, `seelikeus/index.html`, `stm-vlm/index.html`

**Interfaces:**
- Consumes: `--md-font-display` from Task 1 expects 'Space Grotesk' to be loaded.

- [ ] **Step 1: Batch-replace font + css links** in all 13 files:
  - `family=Playfair+Display:wght@400;600;700` → `family=Space+Grotesk:wght@500;600;700` (some pages may use slightly different Playfair weight lists — replace the whole `family=Playfair+Display[^&"]*` segment; keep the Inter segment).
  - `/css/material-design.css` → `/css/material-design.css?v=3` (also match relative `../css/` or `css/` forms if any page uses them).
  Use `python3` script or repeated `Edit` calls; verify each file changed.

- [ ] **Step 2: Verify** `grep -rl "Playfair" --include=index.html . | grep -v music` → empty; `grep -rl "material-design.css?v=3" --include=index.html . | grep -v music | wc -l` → 13.

- [ ] **Step 3: Commit** `git add -A ':!music' ':!My CV.pdf' && git commit -m "redesign: Space Grotesk font + cache-busted stylesheet links"`

---

### Task 4: Homepage inline styles — dark restyle

**Files:**
- Modify: `index.html` (inline `<style>` block + a few inline attributes)

- [ ] **Step 1: Section-alt + contact.** `.section-alt { background-color: var(--md-surface-dim); }`. Replace the `.homepage-contact-dark` rule set with: `.homepage-contact-dark { background-color: var(--md-surface-dim); }` and delete its h2/p/strong/social white overrides (tokens handle it); keep `.homepage-contact-dark h2::after { background-color: var(--md-tertiary); }`.

- [ ] **Step 2: Hero details.** `.hero-affiliation { color: var(--md-tertiary); opacity: 1; }` (keep size/spacing lines). `.md-hero--profile { min-height: 65vh; }` (update existing 70vh override; mobile stays 76vh). Hero social links keep white styling (dark hero). Avatar border stays.

- [ ] **Step 3: Email image frame.** On the contact email `<img>`, replace the style attribute with `style="display: inline-block; background: #eef1ff; padding: 8px; border-radius: var(--md-shape-sm);"`.

- [ ] **Step 4: Footer.** Replace footer inline `style="background-color: var(--md-inverse-surface); color: var(--md-inverse-on-surface); ..."` with `style="background-color: #080d22; color: #9aa6d8; text-align: center; padding: 32px 0;"`. `.footer-links a { color: #9aa6d8; }`.

- [ ] **Step 5: Timeline/pub-row check.** These use tokens — confirm `.homepage-pub-list { background-color: var(--md-surface); }` renders as dark card list; change to `var(--md-surface-variant)`? No — keep `var(--md-surface)`: rows sit on alt/base alternation; set `.homepage-pub-list { background-color: var(--md-surface-variant); }` and row hover `background-color: rgba(238,241,255,0.05);`.

- [ ] **Step 6: Verify in Chrome** (serve :8799): full scroll 1440px — hero gradient, gold role line, dark sections alternate, readable rows/chips, contact dark, footer near-black. Check console clean.

- [ ] **Step 7: Commit** `git add index.html && git commit -m "redesign: homepage dark restyle"`

---

### Task 5: icsr-2026 — recolor teal identity into indigo family

**Files:**
- Modify: `icsr-2026/index.html` (inline styles/hero gradient)

- [ ] **Step 1:** Find its hero/section custom colors (`grep -n "gradient\|#0d9488\|#14b8a6\|#0f766e\|teal\|icsr-" icsr-2026/index.html`). Replace the hero gradient with `linear-gradient(135deg, #111a3e 0%, #1c2a5e 55%, #3d53c6 100%)`; any teal accents → `#7c93ff`; section-title underline → `var(--md-tertiary)`; `.icsr-footer` background → `#080d22`.

- [ ] **Step 2: Verify** page in Chrome: cohesive indigo, still distinct (brighter gradient) from paper pages.

- [ ] **Step 3: Commit** `git add icsr-2026/index.html && git commit -m "redesign: icsr-2026 indigo recolor"`

---

### Task 6: Paper pages — hardcoded light-color audit + fixes

**Files:**
- Modify: any of the 11 paper pages with hardcoded light styles (known: `post-training-vs-continual-learning/index.html`, `har-agent-tmm/index.html`, `har-agent/index.html`, `brlbam/index.html`; audit all)

- [ ] **Step 1: Audit.** For each page run `grep -nE "#[0-9a-fA-F]{3,6}|rgba?\(" <page> | grep -v "var(--" ` and classify. Apply this mapping to page-level styles (NOT to `.md-figure` images, hero status pill greens `#a7f3d0`, or brand colors):
  - white/near-white backgrounds (`#fff`, `#ffffff`, `#f8fafc`, `#f9fafb`) → `var(--md-surface-variant)` (or keep `#ffffff` when it frames an image)
  - light blue tints (`#e3f2fd`, `#eff6ff`, `#dbeafe`, `#e0f2fe`) → `var(--md-primary-container)` with text `var(--md-on-primary-container)`
  - dark text colors (`#0f172a`, `#1e293b`, `#334155`) → `var(--md-on-surface)`
  - medium gray text (`#475569`, `#64748b`) → `var(--md-on-surface-variant)`
  - light borders (`#e2e8f0`, `#e5e7eb`) → `var(--md-outline-variant)`
  - `color: white`/`#fff` on filled-primary elements → `var(--md-on-primary)`
- [ ] **Step 2:** Known specific fixes: `har-agent-tmm` `.md-btn-close { background: var(--md-primary); color: white; }` → `color: var(--md-on-primary);`; `post-training` `.at-a-glance`/`.citation-block`/`.results-table thead` use `var(--md-surface-variant)` (already token-driven — confirm contrast only).
- [ ] **Step 3: Verify** each fixed page in Chrome at 1440px: no light patches, all text readable, tables/boxes dark, figures white-framed.
- [ ] **Step 4: Commit** `git add -A ':!music' ':!My CV.pdf' && git commit -m "redesign: recolor hardcoded light styles on paper pages"`

---

### Task 7: 404 page — Midnight Indigo variant

**Files:**
- Modify: `404.html` (`:root` variables only; `body.music-404` block untouched)

- [ ] **Step 1:** Replace the `:root` block with:

```css
:root {
  --bg-start: #0c1330;
  --bg-end: #182456;
  --accent: #7c93ff;
  --accent-soft: rgba(124, 147, 255, 0.15);
  --text: #eef1ff;
  --text-dim: #9aa6d8;
}
```

- [ ] **Step 2: Verify** `404.html` in Chrome (both plain and a `/music/...` path via the GitHub-Pages-mimic server if convenient — plain check is sufficient since music vars are untouched).
- [ ] **Step 3: Commit** `git add 404.html && git commit -m "redesign: 404 Midnight Indigo variant"`

---

### Task 8: Full verification pass

**Files:** none (fixes fold into this task if found)

- [ ] **Step 1:** `git status --short music/` → empty (music untouched).
- [ ] **Step 2:** `grep -rn "border-radius: 50%\|border-radius: 50px\|shape-full)" --include="*.html" --include="*.css" . | grep -v music | grep -v "\-\-md-shape-full:"` → empty.
- [ ] **Step 3:** Chrome sweep, served locally: all 13 pages + 404 at 1440×900 and 390×844 — screenshot top + one mid-scroll each; console errors = 0; no horizontal overflow; light-fallback smoke test with emulated `prefers-color-scheme: light` on homepage.
- [ ] **Step 4:** Fix anything found; commit fixes as `redesign: verification fixes`.
- [ ] **Step 5:** Report to user; push only when user confirms (SSH or WebStorm).
