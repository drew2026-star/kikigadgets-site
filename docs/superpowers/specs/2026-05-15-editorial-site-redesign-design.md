# Kiki Gadgets — Editorial Site Redesign

**Date:** 2026-05-15
**Status:** Design approved, awaiting plan

## Goal

Redesign the existing Kiki Gadgets static site with a cohesive "editorial / magazine" aesthetic, and add per-app detail pages so each app has a marketing landing page (linked from the home page) with content sourced from its App Store / Play Store listing.

Fix the broken Meal Lineup store links, remove the "Coming Soon" badge (the app is released), and bring the privacy pages into the new design language (legal content and URLs unchanged).

## Scope

Five pages, sharing one stylesheet:

| Page | File | Action |
|---|---|---|
| Home | `index.html` | Redesign + fix Meal Lineup store links + remove "Coming Soon" |
| Meal Lineup detail | `app-meal-lineup.html` | New |
| Chore Scramble detail | `app-chore-scramble.html` | New |
| Meal Lineup privacy | `privacy-meal-lineup.html` | Redesign only — URL and legal content preserved verbatim |
| Chore Scramble privacy | `privacy-chore-scramble.html` | Redesign only — URL and legal content preserved verbatim |

Single shared `style.css` rewrite. No build step, no framework — same plain static-site shape as today.

## Design System

Validated by approved mockup (`a-editorial.html` in `.superpowers/brainstorm/`).

**Typography**
- Display: **Fraunces** (variable; soft + italic axes used)
- Body: **Spectral**
- Both loaded from Google Fonts with `display=swap`

**Color tokens (in `:root`)**
- `--paper: #F4EEE3` — primary background
- `--paper-deep: #ECE3D1` — alternate surface
- `--ink: #1B1611` — primary text
- `--ink-soft: #4A3F33` — secondary text
- `--terracotta: #B0492F` — single shared accent (per user decision)
- `--olive: #7A7A45` — used sparingly in phone mockups, never as primary accent
- `--rule: rgba(27,22,17,0.18)` — hairline dividers

**Texture**
- Fixed-position SVG noise overlay at low opacity (the "paper" feel)

**Shared components (CSS classes)**
- `.masthead` — site nav: back link / wordmark / right meta
- `.issue` — small caps strip below masthead ("Established MMXXVI · An Independent Studio · …")
- `.kicker` — italic terracotta lead-in over headlines
- `.headline` — large Fraunces with `em` for italic accent
- `.deck` — italic Spectral subhead
- `.intro` — drop-cap intro paragraph (first paragraph styled with `::first-letter`)
- `.chapters` + `.chapter` — numbered feature list (I, II, III…)
- `.pullquote` — centered large italic quote with rule above
- `.acquisition` — two-column block: text on left, store buttons on right
- `.store-link` — dark ink store button with SVG icon + label/name
- `.phone` — simulated phone frame for in-line app UI mockups
- `.colophon` — small-caps footer with "Set in Fraunces & Spectral" mark

## Per-Page Treatment

### Home (`index.html`)

- Masthead + issue strip
- Editorial hero: kicker + Fraunces headline (new tagline TBD — options below) + Spectral deck + small byline
- Section: "The Apps" — Fraunces section heading, then a stacked layout where each app gets its own editorial "spread":
  - Spread structure: phone mockup on alternating sides, app name in Fraunces, deck line, three-bullet feature teaser, "Read more →" linking to that app's detail page, plus a "Privacy" sub-link
  - Meal Lineup spread (phone on right) — **no "Coming Soon" badge**
  - Chore Scramble spread (phone on left)
- Section: "About" — keep existing About copy verbatim, retypeset in the editorial system
- Colophon footer with contact email + privacy links

### App detail — Meal Lineup (`app-meal-lineup.html`)

Same structure as the approved mockup, with real content from the App Store listing:
- Masthead + issue strip
- Hero: kicker, headline ("A weekly meal plan, *without* the Sunday-night dread."), deck pulling from "Family weekly meal planner with recipes, smart grocery lists, and Kroger/Walmart sync.", phone mockup with simulated weekly-lineup UI
- Intro: drop-cap two-column intro paragraph
- Chapters: six numbered features —
  - I. 5,000+ recipes (Spoonacular)
  - II. AI grocery list, aisle-sorted
  - III. Kroger & Walmart direct sync
  - IV. Web recipe import
  - V. Nutrition coverage
  - VI. Community recipes
- Pull quote
- Acquisition: both store buttons (App Store + Play Store) + privacy link
- Colophon

### App detail — Chore Scramble (`app-chore-scramble.html`)

Same structure. Content pulled from Play Store listing (separate fetch needed during implementation — the initial WebFetch was truncated). Likely chapter outline based on what we already know:
- Random chore assignment / spin-the-wheel
- Family member profiles
- Progress tracking
- Reward system
- Plus whatever else the Play Store listing reveals

Phone mockup simulates a chore-wheel / family-profile screen.
Acquisition block: Play Store button only (no App Store listing yet), privacy link.

### Privacy pages (`privacy-meal-lineup.html`, `privacy-chore-scramble.html`)

- Same masthead + issue strip + colophon
- Reading layout: single column, max-width ~720px, Spectral body, generous line-height
- Title in Fraunces ("Meal Lineup — Privacy Policy")
- Drop-cap on the first body paragraph
- Section headings in Fraunces; horizontal-rule hairlines between major sections (uses existing `<h2>` structure)
- Lists and inline links restyled to the editorial palette
- **All legal text preserved verbatim** — only the typography, spacing, and layout change

## Behaviors and Fixes

- Remove `<span class="coming-soon">Coming Soon!</span>` from Meal Lineup
- Replace `href="#"` Meal Lineup store buttons with the real URLs:
  - App Store: `https://apps.apple.com/us/app/meal-lineup-recipes-planner/id6766699144`
  - Play Store: `https://play.google.com/store/apps/details?id=com.drewi.mealprep`
- Chore Scramble keeps its existing Play Store URL; no App Store button on its card or detail page
- Home-page app cards link to the corresponding detail page (`app-meal-lineup.html`, `app-chore-scramble.html`) — not directly to the store
- Phone mockups in this iteration are simulated UI built in CSS/HTML. Real App Store / Play Store screenshots are a follow-up task once the structure is in place

## Out of Scope

- Real app screenshots (deferred to follow-up)
- Animations beyond CSS hover/transition micro-interactions already in the mockup
- Any change to privacy-policy legal text or its URL
- Build tooling (still plain static site)
- Per-app accent variation (decided against — one shared terracotta)
- Backend, analytics, contact form

## Open Items

**Home-page tagline.** The current hero says "Apps Built for Everyday Life." User has asked for a new one in the editorial voice. Three options to pick from at the start of implementation:

1. **"Small apps for the long Tuesday."** — quiet, specific, lived-in.
2. **"A studio for everyday tools."** — declarative, brand-forward.
3. **"Practical software, set in serifs."** — winks at the design itself; more playful.

Decision needed before the home page is wired up.

**Chore Scramble content.** The Play Store fetch was truncated. During implementation, we'll need a more targeted fetch (or browser-based read) to pull the real description and feature list. If it can't be obtained cleanly, we fall back to the feature list already on the home card and the user's own words.

## Implementation Status

Implemented 2026-05-15. All five pages live with shared editorial system. Real app screenshots remain a follow-up task — currently using simulated UI mockups.
