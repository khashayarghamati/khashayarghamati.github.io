# Poem-Search SEO Enrichment — Persian Blues Sessions track pages

**Date:** 2026-08-12 · **Approved:** Full enrichment option
**Goal:** rank track pages for searches about the underlying classical Persian poems
(exact hemistichs, «معنی شعر…» queries, poem + آهنگ queries, English equivalents).
Realistic target is long-tail top-5; head terms are owned by Ganjoor/Wikipedia.

## Problem

Track pages rank only for music queries because:
1. The searched string (poem opening line) is absent from `<title>`/meta description.
2. Pages are thin — no prose about the poem, its source work, or meaning
   (matches GSC "Crawled – currently not indexed").
3. JSON-LD has no poem entity, no poet `sameAs` (Knowledge Graph linkage),
   no `isBasedOn` connecting recording → poem.

## Design (implemented in PersianBluse source repo, deployed via deploy-music.sh)

### Data model (`src/lib/types.ts`)
Optional per-track block:
```ts
poem?: {
  title: Bilingual;                      // e.g. "Ney-nameh" / «نی‌نامه»
  source: Bilingual;                     // "Masnavi, Book One" / «مثنوی معنوی، دفتر اول»
  about: { en: string[]; fa: string[] }; // 2–3 paragraphs, rendered on page
  keywords: { en: string[]; fa: string[] };
}
```

### Poet entity registry (`src/lib/poets.ts`)
Map keyed by `poet.name.en` → `sameAs` array (English + Persian Wikipedia URLs).
Wikipedia only — stable, verifiable; no guessed Wikidata IDs.

### Page template (`tracks/[slug]/page.tsx`)
- **Meta description** gains the poem opening line: `«{poemExcerpt}» — {themeSummary[l]}`.
- **Keywords** metadata from `poem.keywords` (en + fa merged).
- **Visible content:** source caption under the poem; new "About the poem"
  section (locale-aware, RTL on fa) rendering `poem.about[l]`.
- **JSON-LD additions:** `CreativeWork` node `#poem` (genre Poetry, `inLanguage: fa`,
  full text, `isPartOf` source work, author Person with `sameAs`);
  `MusicRecording.isBasedOn → #poem`; poet nodes get `sameAs`.
- New i18n label `track.aboutPoemLabel` (en/fa).

### Content (all 6 tracks)
| Track | Source work stated |
|---|---|
| the-song-of-the-reed | نی‌نامه — مثنوی معنوی، دفتر اول |
| the-gambler | غزلیات شمس تبریزی |
| the-saqis-cup | غزل شمارهٔ ۱ دیوان حافظ |
| the-thirty-years | ابیات مشهور منسوب به فردوسی (شاهنامه) — hedged, authenticity debated |
| joyful-world | غزلیات سعدی |
| say-nothing-else | غزلیات شمس تبریزی |

Poems are public domain (10th–14th c.); full text already on page.

## Out of scope
English verse translations (rejected — Persian-script-only identity), FAQ schema,
separate poem pages.

## Verification
Build via deploy-music.sh; grep built HTML for excerpt-in-description, keywords,
`#poem` JSON-LD, sameAs; commit source + Pages repos; user pushes.
