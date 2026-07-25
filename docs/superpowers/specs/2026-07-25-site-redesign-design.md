# ghamati.com UI Redesign — Design Spec

**Date:** 2026-07-25
**Scope:** All pages except `/music/` (untouched) — `index.html`, 12 publication/session pages, `404.html`, shared `css/material-design.css`.
**Decided with user via visual companion:** Midnight Indigo colour world · Modern Grotesk typography · Refined Scroll layout · dark-first.

## Goal

Reposition the site's look from "light corporate MD3" to a modern, impressive, dark-first identity that matches a Principal AI Engineer, without changing content, section structure, or JS behaviour. This is a re-theme of the existing shared design system plus a targeted per-page pass — not a rebuild.

## Approach

Re-theme `css/material-design.css` in place: rewrite the token layer to be dark-by-default and restyle shared components once, so all pages transform together. Then a per-page pass fixes hardcoded light-mode inline styles. Rejected alternative: clean-slate rebuild of all pages (~10× effort, high regression risk on ~8,900 lines of bespoke HTML, same visual outcome).

## 1. Design system (token layer)

### Colours — dark default (`:root`)

| Role | Value |
|---|---|
| Background (`--md-surface`) | `#0C1330` |
| Raised surface / cards (`--md-surface-variant`) | `#141D45` |
| Section-alt background (`--md-surface-dim`, new distinct token — currently `.section-alt` shares `--md-surface-variant` with cards; they must diverge so cards remain visible on alt sections) | `#0F1738` |
| Borders / outlines (`--md-outline-variant`) | `#253266`; stronger outline `#33407A` |
| Text primary (`--md-on-surface`) | `#EEF1FF` |
| Text secondary (`--md-on-surface-variant`) | `#9AA6D8` |
| Primary accent (`--md-primary`) | `#7C93FF` periwinkle; on-primary `#0C1330` |
| Primary container | `#253266`; on-primary-container `#C3CDF5` |
| Tertiary / gold (`--md-tertiary`) | `#C9A45C` — section-title underlines, timeline dots, small highlights only |
| Hero gradient | `linear-gradient(150deg, #0C1330 0%, #111A3E 55%, #182456 100%)` |
| Footer background | `#080D22` |
| Status: published | text `#4ADE80` on `#123C2B` |
| Status: under review | text `#FBBF24` on `#3E2E10` |
| Error | `#FCA5A5` family (existing dark values) |

### Light fallback

`@media (prefers-color-scheme: light)` overrides with a light variant: bg `#FAFAF7`, surface `#FFFFFF`, text `#101430`, primary `#3D53C6` (darkened periwinkle for contrast), gold `#B45309`, borders `#E3E6F2`. Dark is the signature; light must merely be clean and readable. (This inverts the current architecture, where light is default and dark is the media-query override.)

### Typography

- Display/headlines: **Space Grotesk** 600–700, letter-spacing `-0.02em` (replaces Playfair Display everywhere).
- Body/UI/labels: **Inter** (unchanged).
- Monospace: only where already present (code/algorithm blocks).
- Google Fonts link updated on every page: drop Playfair, add Space Grotesk (weights 500;600;700).
- Type scale values (sizes/line-heights) unchanged from current MD3 scale.

### Shape, elevation, motion

- Rectangular only (standing user rule): radii 4/8/12px; no `border-radius: 50%`/full anywhere outside `/music/`.
- Dark elevation = surface colour + 1px border + subtle black shadow; hover = border brightens toward `#33407A`/primary + existing `translateY(-2px)` lift. Remove blue-tint gradient overlays (`rgba(26,95,180,…)`) — replace with flat surface colours.
- Motion tokens, reveal-on-scroll, scrollspy, FAB, reduced-motion guards: unchanged.

## 2. Homepage (`index.html`)

Section order unchanged: hero → nav → About → Experience → Education → Research → Publications → Skills → Projects → Teaching → Awards → Contact → footer.

- **Hero:** ~65vh on the indigo gradient. Gold uppercase role line ("PRINCIPAL AI ENGINEER — INFRAMATIC · UNIVERSITY OF HERTFORDSHIRE"), Space Grotesk name (tight, large), tagline, periwinkle filled CTA (`Experience`) + outlined CTA (`Publications`), social icons, avatar keeps `shape-xl` rounded square.
- **Nav:** dark glass (`rgba(12,19,48,0.85)` + blur), periwinkle scrollspy underline.
- **Sections:** alternate `#0C1330` / `#0F1738`. Section titles in Space Grotesk with gold 40×3px underline.
- **Experience timeline:** unchanged structure; gold dots, periwinkle meta line, rule `#253266`.
- **Publications:** dark bordered list; rows separated by `#253266`; recoloured status pills (green/amber above); titles periwinkle.
- **Research chips / skills chips:** outlined dark chips (`#33407A` border, `#C3CDF5` text).
- **Contact:** no special dark box any more — same dark background as the rest; email image gets a light padded frame (`#EEF1FF` background, 8px padding, 8px radius) so the dark-text PNG stays legible.
- **Footer:** `#080D22`, existing link row.

## 3. Publication pages, icsr-2026, 404

- All 12 paper/session pages inherit hero gradient, dark surfaces, typography from the shared CSS automatically.
- Per-page audit pass fixes anything hardcoded for light mode: table styles, highlight/info boxes, figure containers, `post-training-vs-continual-learning` article styles (lede, pull-quote, at-a-glance, citation block), `har-agent-tmm` dialog styles, `icsr-2026` teal hero and section styling — recoloured into the indigo family (keep its identity as a "special session" page but on the same palette).
- **Figures/diagrams:** white-background images get a white frame (`background:#FFFFFF; padding:12px; border-radius:8px`) so diagrams remain legible on dark. Applied via the shared `.md-figure` style + spot checks per page.
- **Hero status pills:** keep; green check `#4ADE80`, review clock `#FBBF24`.
- **404.html:** academic variant recoloured to Midnight Indigo tokens; the `/music/` variant (gold on Persian blue) untouched.

## 4. Delivery

- Cache-busted stylesheet link on all pages: `/css/material-design.css?v=3` (fixes GitHub Pages' 4-hour CSS cache serving stale styles after deploys).
- Add `.superpowers/` and `My CV.pdf` to `.gitignore`.
- Commit as a small series: (1) tokens + shared components, (2) homepage, (3) sub-pages + 404 + cache-bust links, (4) verification fixes. Push after user verification.

## 5. Verification

- Chrome pass on all 13 pages + 404 at 1440px and 390px: layout, overflow, console errors.
- Contrast spot-checks: body text and secondary text meet WCAG AA on `#0C1330`/`#141D45` (`#EEF1FF` ≈ 15:1, `#9AA6D8` ≈ 7:1 — pass); status pill text on containers ≥ 4.5:1.
- Light-fallback smoke test via emulated `prefers-color-scheme: light`.
- Verify `/music/` byte-identical (git diff empty under `music/`).

## Out of scope

- `/music/` — any file under it.
- Content/copy changes, section reordering, new sections.
- JS behaviour changes (scrollspy, FAB, reveal, dialogs stay as-is).
- SEO/meta/JSON-LD changes.
