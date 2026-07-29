# Kreuk-Style Structure on Midnight Indigo — Design

**Date:** 2026-07-29
**Status:** Approved by user ("yes, implement it")

## Context

User asked whether ghamati.com should look like felixkreuk.github.io (minimal white
academic template). Decision: **do not copy the aesthetic** — it contradicts the
Principal-AI-Engineer positioning and would erase the Midnight Indigo identity.
Instead, borrow its *discipline*: brevity, year grouping, inline resource links.
Also adopt the new portrait `images/Khashayar.webp`.

## Scope (homepage `index.html` only + one image export)

1. **New portrait.** Hero avatar uses `images/Khashayar.webp` (same rectangular
   `shape-xl` frame). Social previews (`og:image`, `twitter:image`, JSON-LD
   `image`) point at a JPG export `images/khashayar-og.jpg` (736×704) because
   LinkedIn/WhatsApp don't reliably render WebP. `profile.jpg` stays in repo.

2. **Year-grouped publications.** Flat `.homepage-pub-list` gains group labels
   (`.homepage-pub-year`: Space Grotesk 700, uppercase, gold `--md-tertiary`,
   surface-dim band): **Under Review → 2026 → 2025 → 2024 → 2023**.
   Rows keep venue pills + title links. In the Under Review group, pills drop
   the redundant "· Under review" suffix. Where a paper has external resources,
   a quiet inline link line (`.homepage-pub-row__links`, dim underlined links,
   periwinkle on hover) sits under the title inside a `__main` column wrapper:
   - isvsicl: DOI (SSRN) · Video — brlbam: Video — seelikeus: DOI —
     har-agent: Video — CAPA-AI: DOI · Video — pllm: DOI · Code —
     rlbam: Video. Others: title only.
   - Commented-out STM-VLM block removed (page /stm-vlm/ still exists, unlinked).

3. **Condensed About.** Three body paragraphs shortened ~40%, same voice and
   facts (Inframatic role, decade of engineering, NOVA thesis June 2026, ARI,
   venues, TCDS reviewing, SS03). Opening line and closing CTA kept.

## Out of scope

Colors, fonts, dark theme, section order, timeline, `/music/`, shared CSS
(no `?v=` bump needed — all changes are homepage-inline).
