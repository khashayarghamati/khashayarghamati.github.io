# Persian Blues Sessions — Brand assets

The mark is a **girih-framed tar medallion**: the Persian eight-point star border
from the cover art, enclosing a *tar* (long-necked lute). The single light-blue
"blues" note sits at the tar's soundhole — the blues, literally at the heart of
the instrument. Palette: gold `#d4af53` on Persian blue `#0e1c46`.

## Files

| File | Use |
|------|-----|
| `mark.svg` | Primary logo mark (vector, scalable). Website, anywhere. |
| `avatar.png` (2160²) | **Instagram / social profile picture.** Square; safe under a circular crop. |
| `favicon.png` (512²) | Simplified mark for small sizes. Also wired into the site as the browser-tab icon. |
| `logo-horizontal.png` | Mark + "Persian Blues Sessions" + "AI-Generated Blues", one line. Transparent. Headers, email signatures. |
| `logo-stacked.png` | Mark above the wordmark. Transparent. Posters, splash. |
| `seal.svg` / `seal.png` / `seal-on-blue.png` | The **"AI·GENERATED MUSIC·SESSIONS" seal.** Use in captions, IG highlight covers, cover-art corners — anywhere the AI disclosure should travel. |
| `og-image.png` (1200×630) | Social-share / link-preview card (bilingual tagline). Also wired into the site's Open Graph metadata. |

## On the website

- `src/components/motifs/BrandMark.tsx` renders the mark live (used in the header
  and the homepage hero). It inherits `currentColor` for the gold, so it recolors
  cleanly.
- `src/app/icon.png` (favicon), `src/app/apple-icon.png`, and
  `src/app/opengraph-image.png` are picked up automatically by Next.js.
- The seal appears in the site footer beside the AI-generation disclosure.

## Regenerating / new sizes

The assets are rendered from an HTML template so the wordmark uses the real fonts
(EB Garamond + Vazirmatn). Ask and I can re-export at any platform size
(YouTube 800×800 channel icon, Spotify 2660×1140 header, Twitter/X 400×400, etc.).

## The "AI-generated" disclosure

Per the project's commitment to honesty, the seal and the "AI-Generated Blues"
line make clear that **the music and vocals are AI-generated** — while the poems
are original, public-domain classical Persian poetry.
