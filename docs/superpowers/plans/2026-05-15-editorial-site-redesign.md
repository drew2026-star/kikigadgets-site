# Editorial Site Redesign — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rebuild the Kiki Gadgets static site in a shared editorial/magazine aesthetic, add two new app detail pages with real store-listing content, fix broken Meal Lineup store links, and remove the "Coming Soon" badge.

**Architecture:** Plain static HTML with one shared `style.css`. No build step, no framework. Single Editorial design system (Fraunces + Spectral, cream paper, terracotta accent) applied uniformly to home, two new app detail pages, and two restyled privacy pages. Privacy legal content is preserved verbatim; only the wrapper structure and typography change.

**Tech Stack:** HTML5, CSS3 (custom properties, variable fonts), Google Fonts. No JS framework. Verification is visual: open files directly in a browser (`file://` works) and compare against the approved mockup at `.superpowers/brainstorm/50156-1778843962/content/a-editorial.html`.

**Spec:** [docs/superpowers/specs/2026-05-15-editorial-site-redesign-design.md](../specs/2026-05-15-editorial-site-redesign-design.md)

---

## File Structure

| File | Action | Responsibility |
|---|---|---|
| `style.css` | Rewrite | Full editorial design system: tokens, masthead, hero, spreads, chapters, phone, privacy |
| `index.html` | Rewrite | Home — masthead, hero, two app spreads, About, colophon |
| `app-meal-lineup.html` | Create | App detail — hero, intro, six chapters, pull quote, acquisition |
| `app-chore-scramble.html` | Create | App detail — same shape, Play Store content |
| `privacy-meal-lineup.html` | Restyle wrapper | New chrome; all legal `<h2>`/`<p>`/`<ul>` preserved verbatim |
| `privacy-chore-scramble.html` | Restyle wrapper | Same — wrapper only |

All pages link Google Fonts in `<head>` via the same two `<link rel>` tags, then `style.css`.

---

## Task 1: Confirm tagline and fetch Chore Scramble content

**Files:** none directly (decisions captured below; optional notes file)

- [ ] **Step 1: Ask the user to choose a tagline**

Present these three options and capture the choice as `TAGLINE` (used in Task 3):

1. *"Small apps for the long Tuesday."*
2. *"A studio for everyday tools."*
3. *"Practical software, set in serifs."*

If the user picks a fourth option, use that instead. Note: tagline is a single line of Fraunces with one phrase wrapped in `<em>` for italic terracotta accent — confirm with the user which phrase gets emphasised.

- [ ] **Step 2: Fetch Chore Scramble Play Store listing**

The earlier brainstorming fetch was truncated. Retry with a tighter prompt:

Use WebFetch on `https://play.google.com/store/apps/details?id=com.chorescramble.app` with prompt:
> "Extract just three things: (1) the long app description text exactly as written, (2) the short description / tagline shown under the title, (3) any rating count. Skip the navigation, footer, and 'similar apps' content."

If still truncated, fall back to: the feature list already on the existing home page (random chore assignment, family member profiles, progress tracking, reward system) + the existing description ("Turn household chores into a game! Assign, shuffle, and track chores for the whole family with a fun spin-the-wheel twist."). These will be sufficient for the detail page.

- [ ] **Step 3: Record content for use in Task 5**

If the fetch succeeded, paste the extracted description and tagline into a working note for Task 5 (either inline in this task's notes, or save to `docs/superpowers/plans/notes-chore-scramble-content.md` and commit). If using fallback content, no notes file needed.

- [ ] **Step 4: Commit notes file (if created)**

```bash
git add docs/superpowers/plans/notes-chore-scramble-content.md
git commit -m "Capture Chore Scramble store content for redesign"
```

---

## Task 2: Replace `style.css` with editorial design system

**Files:**
- Modify: `style.css` (full rewrite)

This rewrite intentionally breaks every existing page's appearance — they will be rebuilt in subsequent tasks. The CSS is self-contained and consumed unchanged by every later page task.

- [ ] **Step 1: Replace contents of `style.css` with the editorial system**

Write the file with exactly this content:

```css
/* ============================================
   Kiki Gadgets — Editorial Stylesheet
   Set in Fraunces & Spectral
   ============================================ */

:root {
  --paper: #F4EEE3;
  --paper-deep: #ECE3D1;
  --ink: #1B1611;
  --ink-soft: #4A3F33;
  --terracotta: #B0492F;
  --olive: #7A7A45;
  --rule: rgba(27,22,17,0.18);
  --rule-strong: rgba(27,22,17,0.32);
  --max: 1300px;
}

* { box-sizing: border-box; margin: 0; padding: 0; }
html { scroll-behavior: smooth; }
html, body { background: var(--paper); color: var(--ink); }
body {
  font-family: 'Spectral', Georgia, serif;
  font-size: 17px;
  line-height: 1.65;
  overflow-x: hidden;
  background-image:
    radial-gradient(circle at 20% 30%, rgba(176,73,47,0.04) 0%, transparent 50%),
    radial-gradient(circle at 80% 70%, rgba(122,122,69,0.05) 0%, transparent 50%);
}
body::before {
  content: "";
  position: fixed; inset: 0; pointer-events: none; z-index: 1000;
  opacity: 0.5;
  background-image: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='200' height='200'><filter id='n'><feTurbulence type='fractalNoise' baseFrequency='0.85' numOctaves='2' stitchTiles='stitch'/><feColorMatrix values='0 0 0 0 0  0 0 0 0 0  0 0 0 0 0  0 0 0 0.06 0'/></filter><rect width='100%25' height='100%25' filter='url(%23n)'/></svg>");
}
a { color: var(--ink); text-decoration: none; transition: color 0.2s; }
a:hover { color: var(--terracotta); }

/* ---- Masthead ---- */
.masthead {
  border-bottom: 1px solid var(--rule);
  padding: 22px 48px;
  display: flex; align-items: center; justify-content: space-between;
  font-family: 'Fraunces', serif;
  font-variation-settings: "SOFT" 30;
  position: sticky; top: 0;
  background: var(--paper);
  z-index: 50;
}
.masthead-back { font-size: 13px; letter-spacing: 0.18em; text-transform: uppercase; font-weight: 500; }
.masthead-title { font-weight: 600; font-style: italic; font-size: 18px; letter-spacing: -0.01em; }
.masthead-right { font-family: 'Spectral', serif; font-size: 12px; letter-spacing: 0.2em; text-transform: uppercase; color: var(--ink-soft); }

/* ---- Issue strip ---- */
.issue {
  padding: 18px 48px;
  border-bottom: 1px solid var(--rule);
  display: flex; justify-content: space-between;
  font-size: 11px; letter-spacing: 0.28em; text-transform: uppercase;
  color: var(--ink-soft);
}
.issue strong { color: var(--terracotta); font-weight: 600; }

/* ---- Hero ---- */
.hero {
  max-width: var(--max);
  margin: 0 auto;
  padding: 72px 48px 32px;
  display: grid; grid-template-columns: 1.15fr 1fr;
  gap: 64px; align-items: end;
}
.kicker {
  font-family: 'Fraunces', serif;
  font-style: italic; color: var(--terracotta);
  font-size: 18px; margin-bottom: 24px;
}
.kicker::before { content: "❦ "; margin-right: 6px; }
.headline {
  font-family: 'Fraunces', serif;
  font-variation-settings: "SOFT" 40, "opsz" 144;
  font-weight: 400;
  font-size: clamp(3rem, 6vw, 6rem);
  line-height: 0.94;
  letter-spacing: -0.035em;
  margin-bottom: 28px;
}
.headline em { font-style: italic; font-weight: 300; color: var(--terracotta); }
.deck {
  font-family: 'Spectral', serif;
  font-style: italic; font-weight: 300;
  font-size: 1.4rem; line-height: 1.45;
  color: var(--ink-soft);
  max-width: 540px;
  margin-bottom: 28px;
}
.byline {
  margin-top: 24px;
  font-size: 11px; letter-spacing: 0.24em; text-transform: uppercase;
  color: var(--ink-soft);
  border-top: 1px solid var(--rule);
  padding-top: 14px;
  max-width: 540px;
}
.byline strong { color: var(--ink); font-weight: 600; }

/* ---- Phone mockup ---- */
.phone-wrap { display: flex; justify-content: center; align-items: flex-end; position: relative; }
.phone-wrap::before {
  content: ""; position: absolute; inset: 10% -5% 0 -5%;
  background: radial-gradient(ellipse at center, rgba(176,73,47,0.18) 0%, transparent 65%);
  z-index: 0;
}
.phone {
  width: 280px; height: 580px;
  background: var(--ink);
  border-radius: 38px; padding: 14px;
  box-shadow: 0 30px 60px -20px rgba(27,22,17,0.4), 0 0 0 1px rgba(27,22,17,0.15);
  position: relative; z-index: 1;
}
.phone-notch {
  position: absolute; top: 14px; left: 50%; transform: translateX(-50%);
  width: 90px; height: 22px; background: var(--ink);
  border-radius: 0 0 16px 16px; z-index: 2;
}
.phone-screen {
  background: var(--paper);
  border-radius: 26px;
  height: 100%;
  padding: 36px 18px 20px;
  overflow: hidden; position: relative;
}

/* Phone — Meal Lineup */
.ml-status {
  display: flex; justify-content: space-between;
  font-family: 'Fraunces', serif;
  font-size: 9px; letter-spacing: 0.1em;
  color: var(--ink-soft); margin-bottom: 18px;
}
.ml-week { font-family: 'Fraunces', serif; font-style: italic; font-size: 11px; color: var(--terracotta); margin-bottom: 4px; }
.ml-title { font-family: 'Fraunces', serif; font-weight: 500; font-size: 22px; line-height: 1.1; margin-bottom: 16px; letter-spacing: -0.02em; }
.ml-day { border-top: 1px solid rgba(27,22,17,0.12); padding: 10px 0; display: flex; gap: 10px; align-items: center; }
.ml-daylabel { font-family: 'Fraunces', serif; font-size: 9px; letter-spacing: 0.16em; text-transform: uppercase; color: var(--ink-soft); width: 30px; }
.ml-meal { font-family: 'Spectral', serif; font-size: 12px; flex: 1; line-height: 1.3; }
.ml-meal small { color: var(--ink-soft); display: block; font-size: 9px; margin-top: 1px; }
.ml-thumb { width: 36px; height: 36px; border-radius: 8px; background: linear-gradient(135deg, var(--terracotta) 0%, #8C3A24 100%); }
.ml-thumb.two { background: linear-gradient(135deg, var(--olive) 0%, #5C5C33 100%); }
.ml-thumb.three { background: linear-gradient(135deg, #C68A2E 0%, #9A6820 100%); }
.ml-thumb.four { background: linear-gradient(135deg, #6B8E4E 0%, #4C6939 100%); }

/* Phone — Chore Scramble */
.cs-status {
  display: flex; justify-content: space-between;
  font-family: 'Fraunces', serif;
  font-size: 9px; letter-spacing: 0.1em;
  color: var(--ink-soft); margin-bottom: 14px;
}
.cs-label { font-family: 'Fraunces', serif; font-style: italic; font-size: 11px; color: var(--terracotta); margin-bottom: 2px; }
.cs-title { font-family: 'Fraunces', serif; font-weight: 500; font-size: 18px; line-height: 1.1; margin-bottom: 14px; letter-spacing: -0.02em; }
.cs-wheel {
  width: 180px; height: 180px;
  border-radius: 50%;
  margin: 0 auto 16px;
  position: relative;
  background: conic-gradient(var(--terracotta) 0 25%, var(--olive) 25% 50%, #C68A2E 50% 75%, #6B8E4E 75% 100%);
  box-shadow: 0 0 0 6px var(--paper), 0 0 0 7px var(--ink);
}
.cs-wheel::after {
  content: ""; position: absolute;
  top: -10px; left: 50%; transform: translateX(-50%);
  border-left: 8px solid transparent;
  border-right: 8px solid transparent;
  border-top: 14px solid var(--ink);
}
.cs-wheel-center {
  position: absolute; inset: 0;
  display: flex; align-items: center; justify-content: center;
  font-family: 'Fraunces', serif; font-style: italic;
  font-size: 13px; color: var(--paper);
  text-shadow: 0 1px 2px rgba(0,0,0,0.3);
  letter-spacing: -0.01em;
}
.cs-task { border-top: 1px solid rgba(27,22,17,0.12); padding: 7px 0; display: flex; gap: 8px; align-items: center; }
.cs-tasker { font-family: 'Fraunces', serif; font-style: italic; font-size: 11px; color: var(--terracotta); width: 56px; flex-shrink: 0; }
.cs-tasktext { font-family: 'Spectral', serif; font-size: 11px; flex: 1; line-height: 1.3; }
.cs-check { font-family: 'Fraunces', serif; font-size: 13px; color: var(--olive); }

/* ---- Drop cap intro ---- */
.intro-section {
  max-width: var(--max);
  margin: 64px auto 0;
  padding: 64px 48px 0;
  border-top: 4px double var(--rule);
}
.intro-grid {
  display: grid; grid-template-columns: 1fr 1fr;
  gap: 48px; padding-top: 32px;
}
.intro p { font-size: 19px; line-height: 1.6; color: var(--ink); margin-bottom: 1em; }
.intro p:first-child::first-letter {
  font-family: 'Fraunces', serif;
  font-weight: 400;
  font-size: 5.5em;
  float: left; line-height: 0.85;
  margin: 6px 10px -4px 0;
  color: var(--terracotta);
}

/* ---- Chapters ---- */
.chapters { max-width: var(--max); margin: 100px auto 0; padding: 0 48px; }
.chapters-heading {
  font-family: 'Fraunces', serif;
  font-weight: 400; font-style: italic;
  font-size: 2.2rem; letter-spacing: -0.02em;
  margin-bottom: 4px;
}
.chapters-sub {
  font-size: 12px; letter-spacing: 0.22em; text-transform: uppercase;
  color: var(--ink-soft); margin-bottom: 56px;
}
.chapter {
  display: grid; grid-template-columns: 80px 1fr 2fr;
  gap: 40px; padding: 36px 0;
  border-top: 1px solid var(--rule);
  align-items: baseline;
}
.chapter:last-child { border-bottom: 1px solid var(--rule); }
.chapter-num {
  font-family: 'Fraunces', serif;
  font-style: italic; font-weight: 300;
  font-size: 2.4rem; color: var(--terracotta); line-height: 1;
}
.chapter-title {
  font-family: 'Fraunces', serif;
  font-weight: 500; font-size: 1.5rem;
  letter-spacing: -0.02em; line-height: 1.15;
}
.chapter-body {
  font-size: 16px; line-height: 1.7;
  color: var(--ink-soft); max-width: 560px;
}
.chapter-body em { color: var(--ink); font-style: italic; }

/* ---- Pull quote ---- */
.pullquote { max-width: 1000px; margin: 100px auto 0; padding: 0 48px; text-align: center; }
.pullquote::before { content: ""; display: block; width: 40px; height: 1px; background: var(--ink); margin: 0 auto 32px; }
.pullquote blockquote {
  font-family: 'Fraunces', serif;
  font-weight: 300; font-style: italic;
  font-size: clamp(1.6rem, 3vw, 2.6rem);
  line-height: 1.18; letter-spacing: -0.02em;
}
.pullquote cite {
  display: block; margin-top: 28px;
  font-family: 'Spectral', serif; font-style: normal;
  font-size: 12px; letter-spacing: 0.24em;
  text-transform: uppercase; color: var(--ink-soft);
}

/* ---- Acquisition / Stores ---- */
.acquisition {
  max-width: var(--max); margin: 100px auto 0;
  padding: 80px 48px;
  border-top: 4px double var(--rule);
  border-bottom: 4px double var(--rule);
  display: grid; grid-template-columns: 1fr 1fr;
  gap: 64px; align-items: center;
}
.acquisition-title {
  font-family: 'Fraunces', serif; font-weight: 400;
  font-size: 3rem; line-height: 1;
  letter-spacing: -0.025em; margin-bottom: 20px;
}
.acquisition-title em { font-style: italic; color: var(--terracotta); }
.acquisition p { font-family: 'Spectral', serif; font-style: italic; font-size: 1.1rem; color: var(--ink-soft); max-width: 420px; }
.stores { display: flex; flex-direction: column; gap: 14px; }
.store-link {
  display: flex; align-items: center; gap: 18px;
  padding: 20px 28px;
  background: var(--ink); color: var(--paper);
  border-radius: 4px;
  font-family: 'Fraunces', serif;
  transition: transform 0.2s;
}
.store-link:hover { transform: translateX(-4px); color: var(--paper); }
.store-link svg { flex-shrink: 0; }
.store-link-label { font-size: 10px; letter-spacing: 0.2em; text-transform: uppercase; opacity: 0.7; display: block; }
.store-link-name { font-size: 18px; letter-spacing: -0.01em; font-weight: 500; display: block; }
.privacy-mention { margin-top: 24px; font-size: 12px; letter-spacing: 0.18em; text-transform: uppercase; color: var(--ink-soft); }
.privacy-mention a { color: var(--terracotta); text-decoration: underline; text-underline-offset: 4px; }

/* ---- Home: app spreads ---- */
.spreads { max-width: var(--max); margin: 80px auto 0; padding: 0 48px; }
.spreads-heading {
  font-family: 'Fraunces', serif;
  font-weight: 400; font-style: italic;
  font-size: 2.2rem; letter-spacing: -0.02em;
  margin-bottom: 4px;
}
.spreads-sub { font-size: 12px; letter-spacing: 0.22em; text-transform: uppercase; color: var(--ink-soft); margin-bottom: 56px; }
.spread { display: grid; grid-template-columns: 1fr 1fr; gap: 64px; align-items: center; padding: 56px 0; border-top: 1px solid var(--rule); }
.spread:last-child { border-bottom: 1px solid var(--rule); }
.spread.reverse .spread-text { order: 2; }
.spread-num { font-family: 'Fraunces', serif; font-style: italic; font-size: 2rem; color: var(--terracotta); line-height: 1; margin-bottom: 12px; }
.spread-name { font-family: 'Fraunces', serif; font-weight: 500; font-size: 2.4rem; letter-spacing: -0.025em; line-height: 1.05; margin-bottom: 12px; }
.spread-deck { font-family: 'Spectral', serif; font-style: italic; font-size: 1.15rem; color: var(--ink-soft); margin-bottom: 24px; max-width: 460px; }
.spread-features { list-style: none; margin-bottom: 28px; }
.spread-features li {
  font-family: 'Spectral', serif; font-size: 0.95rem;
  color: var(--ink-soft);
  padding: 8px 0;
  border-bottom: 1px dotted var(--rule);
  display: flex; gap: 14px; align-items: baseline;
}
.spread-features li::before {
  content: "·"; color: var(--terracotta);
  font-family: 'Fraunces', serif; font-size: 1.5rem; line-height: 0.7;
}
.spread-readmore {
  font-family: 'Fraunces', serif; font-style: italic; font-size: 1.1rem;
  color: var(--terracotta);
  border-bottom: 1px solid var(--terracotta);
  padding-bottom: 2px;
}
.spread-readmore:hover { color: var(--ink); border-color: var(--ink); }
.spread-privacy {
  display: inline-block; margin-left: 24px;
  font-size: 11px; letter-spacing: 0.18em; text-transform: uppercase;
  color: var(--ink-soft);
}

/* ---- About ---- */
.about {
  max-width: var(--max); margin: 100px auto 0;
  padding: 80px 48px;
  border-top: 4px double var(--rule);
}
.about-heading { font-family: 'Fraunces', serif; font-weight: 400; font-style: italic; font-size: 2.2rem; letter-spacing: -0.02em; margin-bottom: 32px; }
.about-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 48px; }
.about p { font-size: 18px; line-height: 1.65; margin-bottom: 18px; color: var(--ink); }
.about p:first-of-type::first-letter {
  font-family: 'Fraunces', serif;
  font-size: 4em; float: left; line-height: 0.85;
  margin: 4px 8px -2px 0;
  color: var(--terracotta);
}
.about-contact {
  margin-top: 24px;
  font-family: 'Fraunces', serif;
  font-style: italic;
  font-size: 1.15rem;
}
.about-contact a { color: var(--terracotta); border-bottom: 1px solid var(--terracotta); padding-bottom: 2px; }

/* ---- Colophon (footer) ---- */
.colophon {
  max-width: var(--max); margin: 60px auto 0;
  padding: 48px;
  border-top: 1px solid var(--rule);
  display: flex; justify-content: space-between; align-items: baseline;
  font-size: 11px; letter-spacing: 0.22em;
  text-transform: uppercase; color: var(--ink-soft);
  flex-wrap: wrap; gap: 16px;
}
.colophon-mark {
  font-family: 'Fraunces', serif; font-style: italic;
  font-size: 18px; letter-spacing: -0.01em;
  text-transform: none; color: var(--ink);
}
.colophon-links { display: flex; gap: 22px; }
.colophon-links a:hover { color: var(--terracotta); }

/* ---- Privacy page ---- */
.privacy {
  max-width: 720px; margin: 0 auto;
  padding: 80px 48px 100px;
}
.privacy-kicker {
  font-family: 'Fraunces', serif; font-style: italic;
  color: var(--terracotta); font-size: 16px;
  margin-bottom: 16px;
}
.privacy h1 {
  font-family: 'Fraunces', serif;
  font-variation-settings: "SOFT" 40;
  font-weight: 400;
  font-size: clamp(2.4rem, 5vw, 3.6rem);
  line-height: 1; letter-spacing: -0.03em;
  margin-bottom: 16px;
}
.privacy .effective {
  font-family: 'Spectral', serif;
  font-style: italic; color: var(--ink-soft);
  border-top: 1px solid var(--rule);
  border-bottom: 1px solid var(--rule);
  padding: 14px 0;
  font-size: 14px;
  margin-bottom: 48px;
  letter-spacing: 0.02em;
}
.privacy h2 {
  font-family: 'Fraunces', serif;
  font-weight: 500;
  font-size: 1.3rem;
  margin: 44px 0 14px;
  letter-spacing: -0.015em;
  color: var(--ink);
}
.privacy p, .privacy li {
  font-family: 'Spectral', serif;
  font-size: 17px; line-height: 1.7;
  color: var(--ink); margin-bottom: 14px;
}
.privacy ul { padding-left: 24px; margin-bottom: 14px; }
.privacy li::marker { color: var(--terracotta); }
.privacy a { color: var(--terracotta); border-bottom: 1px solid var(--rule); padding-bottom: 1px; }
.privacy a:hover { border-color: var(--terracotta); }
.privacy hr { border: none; border-top: 1px solid var(--rule); margin: 40px 0; }
.privacy strong { font-weight: 600; color: var(--ink); }
.privacy > p:first-of-type::first-letter,
.privacy-intro::first-letter {
  font-family: 'Fraunces', serif;
  font-size: 4.2em; float: left; line-height: 0.85;
  margin: 6px 10px -4px 0; color: var(--terracotta);
}

/* ---- Responsive ---- */
@media (max-width: 900px) {
  .hero, .intro-grid, .acquisition, .spread, .about-grid { grid-template-columns: 1fr; gap: 40px; }
  .spread.reverse .spread-text { order: 0; }
  .chapter { grid-template-columns: 1fr; gap: 12px; }
  .chapter-num { font-size: 1.6rem; }
  .masthead, .issue, .hero, .intro-section, .chapters, .acquisition, .spreads, .about, .colophon, .privacy { padding-left: 24px; padding-right: 24px; }
  .masthead-right, .issue span:nth-child(3) { display: none; }
  .colophon { flex-direction: column; align-items: flex-start; }
}
```

- [ ] **Step 2: Confirm the file is syntactically valid**

Run:
```bash
node -e "const fs=require('fs');const s=fs.readFileSync('style.css','utf8');if(s.includes(':root')&&s.includes('--paper')&&s.includes('@media'))console.log('ok');else process.exit(1);"
```

Or simply confirm visually that `:root { --paper: ...` appears at the top and the file ends with a closing `}` after the `@media` block.

- [ ] **Step 3: Commit**

```bash
git add style.css
git commit -m "Rewrite style.css as editorial design system"
```

---

## Task 3: Rewrite `index.html` (home page)

**Files:**
- Modify: `index.html` (full rewrite)

Uses the `TAGLINE` chosen in Task 1. Below uses option 1 ("Small apps for the long Tuesday.") as a placeholder — substitute the actual choice.

- [ ] **Step 1: Replace contents of `index.html`**

Write the file as follows (substitute the agreed tagline + emphasised phrase in the `<h1>`):

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Kiki Gadgets Ltd. — An Independent App Studio</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Fraunces:ital,opsz,wght,SOFT@0,9..144,300..900,30;0,9..144,300..900,100;1,9..144,400..700,50&family=Spectral:ital,wght@0,300;0,400;0,500;0,600;1,400&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="style.css" />
</head>
<body>

<header class="masthead">
  <a href="index.html" class="masthead-back">Kiki Gadgets, Ltd.</a>
  <div class="masthead-title">An Independent App Studio</div>
  <div class="masthead-right">Est. MMXXVI</div>
</header>

<div class="issue">
  <span>Vol. I, № 1</span>
  <span>The Everyday Issue</span>
  <span>Printed in code</span>
</div>

<section class="hero">
  <div>
    <div class="kicker">A small studio in two titles</div>
    <h1 class="headline">Small apps for the <em>long Tuesday.</em></h1>
    <p class="deck">Practical, well-crafted mobile tools for family life — meal planning today, weekly chores by way of a spin-wheel, with quieter things to come.</p>
    <a href="#apps" class="spread-readmore">See the apps →</a>
    <div class="byline"><strong>Kiki Gadgets Ltd.</strong> · iPhone &amp; Android · support@kikigadgetsltd.com</div>
  </div>
  <div class="phone-wrap">
    <div class="phone">
      <div class="phone-notch"></div>
      <div class="phone-screen">
        <div class="ml-status"><span>9:41</span><span>●●●● 5G</span></div>
        <div class="ml-week">Week of May 18</div>
        <div class="ml-title">This Week's Lineup</div>
        <div class="ml-day"><div class="ml-daylabel">MON</div><div class="ml-meal">Tomato &amp; Lentil Stew<small>Serves 4 · 35 min</small></div><div class="ml-thumb"></div></div>
        <div class="ml-day"><div class="ml-daylabel">TUE</div><div class="ml-meal">Herb-Roasted Chicken<small>Serves 4 · 50 min</small></div><div class="ml-thumb two"></div></div>
        <div class="ml-day"><div class="ml-daylabel">WED</div><div class="ml-meal">Sheet-Pan Salmon<small>Serves 4 · 25 min</small></div><div class="ml-thumb three"></div></div>
        <div class="ml-day"><div class="ml-daylabel">THU</div><div class="ml-meal">Olive Pasta Bowl<small>Serves 4 · 20 min</small></div><div class="ml-thumb four"></div></div>
        <div class="ml-day"><div class="ml-daylabel">FRI</div><div class="ml-meal">Family Tacos<small>Serves 4 · 30 min</small></div><div class="ml-thumb"></div></div>
      </div>
    </div>
  </div>
</section>

<section id="apps" class="spreads">
  <h2 class="spreads-heading">The Apps</h2>
  <div class="spreads-sub">Two titles · Available now</div>

  <article class="spread">
    <div class="spread-text">
      <div class="spread-num">I.</div>
      <h3 class="spread-name">Meal Lineup</h3>
      <p class="spread-deck">A weekly meal plan, without the Sunday-night dread.</p>
      <ul class="spread-features">
        <li>5,000+ recipes via Spoonacular</li>
        <li>AI grocery list, sorted by store aisle</li>
        <li>Kroger &amp; Walmart cart sync</li>
      </ul>
      <a href="app-meal-lineup.html" class="spread-readmore">Read the issue →</a>
      <a href="privacy-meal-lineup.html" class="spread-privacy">Privacy</a>
    </div>
    <div class="phone-wrap">
      <div class="phone">
        <div class="phone-notch"></div>
        <div class="phone-screen">
          <div class="ml-status"><span>9:41</span><span>●●●● 5G</span></div>
          <div class="ml-week">Week of May 18</div>
          <div class="ml-title">This Week's Lineup</div>
          <div class="ml-day"><div class="ml-daylabel">MON</div><div class="ml-meal">Tomato &amp; Lentil Stew<small>Serves 4 · 35 min</small></div><div class="ml-thumb"></div></div>
          <div class="ml-day"><div class="ml-daylabel">TUE</div><div class="ml-meal">Herb-Roasted Chicken<small>Serves 4 · 50 min</small></div><div class="ml-thumb two"></div></div>
          <div class="ml-day"><div class="ml-daylabel">WED</div><div class="ml-meal">Sheet-Pan Salmon<small>Serves 4 · 25 min</small></div><div class="ml-thumb three"></div></div>
          <div class="ml-day"><div class="ml-daylabel">THU</div><div class="ml-meal">Olive Pasta Bowl<small>Serves 4 · 20 min</small></div><div class="ml-thumb four"></div></div>
          <div class="ml-day"><div class="ml-daylabel">FRI</div><div class="ml-meal">Family Tacos<small>Serves 4 · 30 min</small></div><div class="ml-thumb"></div></div>
        </div>
      </div>
    </div>
  </article>

  <article class="spread reverse">
    <div class="phone-wrap">
      <div class="phone">
        <div class="phone-notch"></div>
        <div class="phone-screen">
          <div class="cs-status"><span>9:41</span><span>●●●● 5G</span></div>
          <div class="cs-label">Saturday morning</div>
          <div class="cs-title">Who's doing what?</div>
          <div class="cs-wheel"><div class="cs-wheel-center">Spin</div></div>
          <div class="cs-task"><div class="cs-tasker">Drew</div><div class="cs-tasktext">Vacuum the living room</div><div class="cs-check">✓</div></div>
          <div class="cs-task"><div class="cs-tasker">Sam</div><div class="cs-tasktext">Take out the trash</div><div class="cs-check">✓</div></div>
          <div class="cs-task"><div class="cs-tasker">Mia</div><div class="cs-tasktext">Load the dishwasher</div><div class="cs-check"></div></div>
          <div class="cs-task"><div class="cs-tasker">Leo</div><div class="cs-tasktext">Sort the laundry</div><div class="cs-check"></div></div>
        </div>
      </div>
    </div>
    <div class="spread-text">
      <div class="spread-num">II.</div>
      <h3 class="spread-name">Chore Scramble</h3>
      <p class="spread-deck">Turn household chores into a game — assign, shuffle, and track for the whole family.</p>
      <ul class="spread-features">
        <li>Random spin-the-wheel chore assignment</li>
        <li>Family member profiles &amp; progress</li>
        <li>Badges and a light reward system</li>
      </ul>
      <a href="app-chore-scramble.html" class="spread-readmore">Read the issue →</a>
      <a href="privacy-chore-scramble.html" class="spread-privacy">Privacy</a>
    </div>
  </article>
</section>

<section id="about" class="about">
  <h2 class="about-heading">About Kiki Gadgets</h2>
  <div class="about-grid">
    <div>
      <p>Kiki Gadgets Ltd. is a small, independent app studio focused on building practical, well-crafted tools for everyday family life. We keep things simple, private, and useful.</p>
    </div>
    <div>
      <p>Have a question or feedback? We'd love to hear from you.</p>
      <p class="about-contact">Write to us at <a href="mailto:support@kikigadgetsltd.com">support@kikigadgetsltd.com</a>.</p>
    </div>
  </div>
</section>

<footer class="colophon">
  <span>© MMXXVI · Kiki Gadgets Ltd.</span>
  <div class="colophon-links">
    <a href="privacy-meal-lineup.html">Meal Lineup Privacy</a>
    <a href="privacy-chore-scramble.html">Chore Scramble Privacy</a>
    <a href="mailto:support@kikigadgetsltd.com">Contact</a>
  </div>
  <span class="colophon-mark">Set in Fraunces &amp; Spectral</span>
</footer>

</body>
</html>
```

Notes:
- The first `<h1>` headline is where the chosen tagline goes. If a different option was picked, replace the inner text and adjust the `<em>` so one phrase gets emphasis.
- The hero phone duplicates the Meal Lineup spread phone — that's intentional; in the spread below we show it again next to the description so the home reads "here's the studio → here's app I → here's app II."
- The "Coming Soon" badge is intentionally absent.

- [ ] **Step 2: Visual verification**

Open the file:
```bash
open index.html
```

Verify against the mockup:
- Masthead with "Kiki Gadgets, Ltd." back link / italic title / "Est. MMXXVI"
- Issue strip below it
- Hero with kicker (terracotta `❦` lead), headline with one phrase in italic terracotta, deck, and phone mockup on the right
- "The Apps" section with two spreads (Meal Lineup first / Chore Scramble reversed)
- About section with drop cap on first paragraph
- Colophon at the bottom
- **No "Coming Soon" badge anywhere**
- Mobile: resize browser to <900px and confirm spreads stack and reverse-spread reorders correctly

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "Rewrite home page in editorial style; remove Coming Soon"
```

---

## Task 4: Create `app-meal-lineup.html`

**Files:**
- Create: `app-meal-lineup.html`

Content sourced from the App Store listing fetched during brainstorming (recipes via Spoonacular, AI grocery list aisle-sorted, Kroger/Walmart sync, web recipe import, nutrition coverage, community recipes).

- [ ] **Step 1: Create `app-meal-lineup.html`**

Write the file with exactly this content:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Meal Lineup — Kiki Gadgets Ltd.</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Fraunces:ital,opsz,wght,SOFT@0,9..144,300..900,30;0,9..144,300..900,100;1,9..144,400..700,50&family=Spectral:ital,wght@0,300;0,400;0,500;0,600;1,400&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="style.css" />
</head>
<body>

<header class="masthead">
  <a href="index.html" class="masthead-back">← Kiki Gadgets</a>
  <div class="masthead-title">Kiki Gadgets, Vol. I</div>
  <div class="masthead-right">№ 01 · The Kitchen Issue</div>
</header>

<div class="issue">
  <span>Established <strong>MMXXVI</strong></span>
  <span>An Independent Studio</span>
  <span>Printed in code</span>
</div>

<section class="hero">
  <div>
    <div class="kicker">A field guide to the family table</div>
    <h1 class="headline">A weekly meal plan, <em>without</em> the Sunday-night dread.</h1>
    <p class="deck">Meal Lineup brings five thousand recipes, an AI-organised grocery list, and direct sync with Kroger &amp; Walmart into a single, unhurried weekly rhythm.</p>
    <div class="byline"><strong>Meal Lineup</strong> · iPhone &amp; Android · By Kiki Gadgets Ltd.</div>
  </div>
  <div class="phone-wrap">
    <div class="phone">
      <div class="phone-notch"></div>
      <div class="phone-screen">
        <div class="ml-status"><span>9:41</span><span>●●●● 5G</span></div>
        <div class="ml-week">Week of May 18</div>
        <div class="ml-title">This Week's Lineup</div>
        <div class="ml-day"><div class="ml-daylabel">MON</div><div class="ml-meal">Tomato &amp; Lentil Stew<small>Serves 4 · 35 min</small></div><div class="ml-thumb"></div></div>
        <div class="ml-day"><div class="ml-daylabel">TUE</div><div class="ml-meal">Herb-Roasted Chicken<small>Serves 4 · 50 min</small></div><div class="ml-thumb two"></div></div>
        <div class="ml-day"><div class="ml-daylabel">WED</div><div class="ml-meal">Sheet-Pan Salmon<small>Serves 4 · 25 min</small></div><div class="ml-thumb three"></div></div>
        <div class="ml-day"><div class="ml-daylabel">THU</div><div class="ml-meal">Olive Pasta Bowl<small>Serves 4 · 20 min</small></div><div class="ml-thumb four"></div></div>
        <div class="ml-day"><div class="ml-daylabel">FRI</div><div class="ml-meal">Family Tacos<small>Serves 4 · 30 min</small></div><div class="ml-thumb"></div></div>
      </div>
    </div>
  </div>
</section>

<section class="intro-section">
  <div class="intro-grid">
    <div class="intro">
      <p>Somewhere between the second cup of coffee and the kids asking "what's for dinner?" most weeks fall apart. Meal Lineup proposes a quieter idea: spend ten minutes on Sunday, then forget about it. Five thousand recipes wait in the library; the app scales each one to your family, drafts a grocery list ordered by store aisle, and hands the whole basket off to Kroger or Walmart.</p>
    </div>
    <div class="intro">
      <p>It is not a recipe book and it is not a smart speaker. It is the part of the week you keep losing — quietly returned, with nutrition coverage logged in the margins and a button labelled "send to cart" right where the panic used to live.</p>
    </div>
  </div>
</section>

<section class="chapters">
  <h2 class="chapters-heading">Inside the issue</h2>
  <div class="chapters-sub">Six chapters · Roughly forty seconds each</div>

  <div class="chapter">
    <div class="chapter-num">I.</div>
    <div class="chapter-title">Five thousand recipes, from Spoonacular</div>
    <div class="chapter-body">Search by ingredient, dietary need, or by the hour you have left. <em>Each recipe scales to your family size</em>, with prep notes that respect a tired Wednesday.</div>
  </div>

  <div class="chapter">
    <div class="chapter-num">II.</div>
    <div class="chapter-title">A grocery list that knows the store</div>
    <div class="chapter-body">An AI sorts your week into aisles — produce first, dairy last, never doubling back. The list never asks where the cumin lives.</div>
  </div>

  <div class="chapter">
    <div class="chapter-num">III.</div>
    <div class="chapter-title">Kroger &amp; Walmart, side door</div>
    <div class="chapter-body">Send the basket straight to your store. Your list becomes a checkout — <em>no copying, no typing, no second app</em>.</div>
  </div>

  <div class="chapter">
    <div class="chapter-num">IV.</div>
    <div class="chapter-title">Recipes brought from anywhere</div>
    <div class="chapter-body">Paste a URL; the app pulls the recipe in clean. The Tuesday-night Instagram pasta is yours by Sunday morning.</div>
  </div>

  <div class="chapter">
    <div class="chapter-num">V.</div>
    <div class="chapter-title">Nutrition, kept in the margin</div>
    <div class="chapter-body">A gentle, weekly read of food-group coverage. The point isn't tracking calories — it's noticing what's missing.</div>
  </div>

  <div class="chapter">
    <div class="chapter-num">VI.</div>
    <div class="chapter-title">A small community, optional</div>
    <div class="chapter-body">Share what worked. Borrow what didn't yet. Make a recipe of your own and put it in the lineup.</div>
  </div>
</section>

<section class="pullquote">
  <blockquote>"The grocery list went from a Sunday chore to something I no longer think about. The app does the thinking; we do the eating."</blockquote>
  <cite>— A note for the cover</cite>
</section>

<section class="acquisition">
  <div>
    <h2 class="acquisition-title">To begin, <em>simply</em><br>open the app store.</h2>
    <p>Free to download. The weekly rhythm starts on whichever Sunday you choose.</p>
    <div class="privacy-mention">Read the <a href="privacy-meal-lineup.html">Privacy Policy</a></div>
  </div>
  <div class="stores">
    <a href="https://apps.apple.com/us/app/meal-lineup-recipes-planner/id6766699144" class="store-link" target="_blank" rel="noopener">
      <svg width="28" height="32" viewBox="0 0 28 32" fill="none" aria-hidden="true"><path d="M22.5 24.5c-1 2.3-2.2 4.5-4 4.5-1.7 0-2.3-1-4.3-1s-2.6 1-4.2 1c-1.8 0-3.1-2-4.1-4.3C3.8 19.7 5.9 14 10 14c1.8 0 2.8 1 4.2 1 1.3 0 2.4-1 4.5-1 1.8 0 3.6 1 4.7 2.7-4.1 2.3-3.5 8.1.1 8.3z" fill="#F4EEE3"/><path d="M17 8c1-1.3 1.5-3 1.3-4.7C16.8 3.4 15 4.3 14 5.6c-.9 1.2-1.6 2.9-1.4 4.5 1.6.1 3.4-.8 4.4-2.1z" fill="#F4EEE3"/></svg>
      <div>
        <span class="store-link-label">Available on</span>
        <span class="store-link-name">the App Store</span>
      </div>
    </a>
    <a href="https://play.google.com/store/apps/details?id=com.drewi.mealprep" class="store-link" target="_blank" rel="noopener">
      <svg width="28" height="32" viewBox="0 0 28 32" fill="none" aria-hidden="true"><path d="M3 2.5v27L17.5 16 3 2.5z" fill="#F4EEE3"/><path d="M22.5 11.5L17.5 16l5 4.5L26 18c1.5-.9 1.5-3.1 0-4l-3.5-2.5z" fill="#F4EEE3" opacity="0.85"/><path d="M3 2.5l14.5 13.5L22.5 11.5 5 1.5c-.7-.4-1.5-.1-2 1z" fill="#F4EEE3" opacity="0.7"/><path d="M3 29.5l14.5-13.5 5 4.5L5 30.5c-.7.4-1.5.1-2-1z" fill="#F4EEE3" opacity="0.55"/></svg>
      <div>
        <span class="store-link-label">Get it on</span>
        <span class="store-link-name">Google Play</span>
      </div>
    </a>
  </div>
</section>

<footer class="colophon">
  <span>© MMXXVI · Kiki Gadgets Ltd.</span>
  <div class="colophon-links">
    <a href="index.html">Home</a>
    <a href="privacy-meal-lineup.html">Privacy Policy</a>
    <a href="mailto:support@kikigadgetsltd.com">Contact</a>
  </div>
  <span class="colophon-mark">Set in Fraunces &amp; Spectral</span>
</footer>

</body>
</html>
```

- [ ] **Step 2: Visual verification**

```bash
open app-meal-lineup.html
```

Confirm against the approved mockup:
- Masthead + issue strip
- Hero with kicker, italic-accented headline, deck, phone mockup
- Two-column drop-cap intro
- Six numbered chapters with `I.` through `VI.` in italic terracotta
- Pull quote centered
- Acquisition block: title on left, App Store + Google Play buttons stacked on right
- Footer colophon
- Click both store buttons and confirm they open the correct URLs in a new tab
- Click "Privacy Policy" link → should go to `privacy-meal-lineup.html` (will look broken still — that's Task 6)
- Mobile: stacks correctly

- [ ] **Step 3: Commit**

```bash
git add app-meal-lineup.html
git commit -m "Add Meal Lineup detail page with App Store content"
```

---

## Task 5: Create `app-chore-scramble.html`

**Files:**
- Create: `app-chore-scramble.html`

Uses Chore Scramble Play Store content from Task 1 (or fallback content). Below uses fallback content. **Substitute the real Play Store description in the hero deck and intro if Task 1's fetch succeeded.**

- [ ] **Step 1: Create `app-chore-scramble.html`**

Write the file with exactly this content:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Chore Scramble — Kiki Gadgets Ltd.</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Fraunces:ital,opsz,wght,SOFT@0,9..144,300..900,30;0,9..144,300..900,100;1,9..144,400..700,50&family=Spectral:ital,wght@0,300;0,400;0,500;0,600;1,400&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="style.css" />
</head>
<body>

<header class="masthead">
  <a href="index.html" class="masthead-back">← Kiki Gadgets</a>
  <div class="masthead-title">Kiki Gadgets, Vol. I</div>
  <div class="masthead-right">№ 02 · The Household Issue</div>
</header>

<div class="issue">
  <span>Established <strong>MMXXVI</strong></span>
  <span>An Independent Studio</span>
  <span>Printed in code</span>
</div>

<section class="hero">
  <div>
    <div class="kicker">A small game for a big house</div>
    <h1 class="headline">Household chores, <em>spun</em> like a wheel.</h1>
    <p class="deck">Chore Scramble turns the family's least-favourite Saturday list into a quick, fair game. Spin to assign, track progress, collect a few badges along the way.</p>
    <div class="byline"><strong>Chore Scramble</strong> · Android · By Kiki Gadgets Ltd.</div>
  </div>
  <div class="phone-wrap">
    <div class="phone">
      <div class="phone-notch"></div>
      <div class="phone-screen">
        <div class="cs-status"><span>9:41</span><span>●●●● 5G</span></div>
        <div class="cs-label">Saturday morning</div>
        <div class="cs-title">Who's doing what?</div>
        <div class="cs-wheel"><div class="cs-wheel-center">Spin</div></div>
        <div class="cs-task"><div class="cs-tasker">Drew</div><div class="cs-tasktext">Vacuum the living room</div><div class="cs-check">✓</div></div>
        <div class="cs-task"><div class="cs-tasker">Sam</div><div class="cs-tasktext">Take out the trash</div><div class="cs-check">✓</div></div>
        <div class="cs-task"><div class="cs-tasker">Mia</div><div class="cs-tasktext">Load the dishwasher</div><div class="cs-check"></div></div>
        <div class="cs-task"><div class="cs-tasker">Leo</div><div class="cs-tasktext">Sort the laundry</div><div class="cs-check"></div></div>
      </div>
    </div>
  </div>
</section>

<section class="intro-section">
  <div class="intro-grid">
    <div class="intro">
      <p>Households have always run on a quiet, slightly unfair arithmetic — someone, often the same someone, ends up with the dishes. Chore Scramble proposes a small intervention: spin the wheel on Saturday morning, take what comes, and call it even by lunch.</p>
    </div>
    <div class="intro">
      <p>It is not a productivity app. There are no streaks, no nudges, no algorithms learning your habits. It is a wheel, a list, and a tally — built for the kind of family that would rather laugh about who has to take out the trash than negotiate it.</p>
    </div>
  </div>
</section>

<section class="chapters">
  <h2 class="chapters-heading">Inside the issue</h2>
  <div class="chapters-sub">Four chapters · A weekend tradition</div>

  <div class="chapter">
    <div class="chapter-num">I.</div>
    <div class="chapter-title">Spin-the-wheel chore assignment</div>
    <div class="chapter-body">Drop the household's chores into the wheel; each spin lands on a person. Take the result, swap if you must, but mostly — <em>just take what comes</em>.</div>
  </div>

  <div class="chapter">
    <div class="chapter-num">II.</div>
    <div class="chapter-title">Family member profiles</div>
    <div class="chapter-body">Each person gets their own corner of the app — a name, an avatar, and a quiet history of what they've done. No accounts, no email. The app stays anonymous on purpose.</div>
  </div>

  <div class="chapter">
    <div class="chapter-num">III.</div>
    <div class="chapter-title">Progress tracking</div>
    <div class="chapter-body">Check off the things that got done. The app remembers — across weeks — who's been keeping up and who is owed an easy week next.</div>
  </div>

  <div class="chapter">
    <div class="chapter-num">IV.</div>
    <div class="chapter-title">A light reward system</div>
    <div class="chapter-body">Badges for the milestones — a hundred dishes, the first month, the longest streak. Just enough to make the seven-year-old proud, not so much that the rest of you find it tedious.</div>
  </div>
</section>

<section class="pullquote">
  <blockquote>"It turned Saturday morning from a negotiation into a game. The kids actually ask to spin the wheel."</blockquote>
  <cite>— A note for the cover</cite>
</section>

<section class="acquisition">
  <div>
    <h2 class="acquisition-title">Available <em>now</em><br>on Google Play.</h2>
    <p>Free to download. Anonymous by design — no email, no account, no fuss.</p>
    <div class="privacy-mention">Read the <a href="privacy-chore-scramble.html">Privacy Policy</a></div>
  </div>
  <div class="stores">
    <a href="https://play.google.com/store/apps/details?id=com.chorescramble.app" class="store-link" target="_blank" rel="noopener">
      <svg width="28" height="32" viewBox="0 0 28 32" fill="none" aria-hidden="true"><path d="M3 2.5v27L17.5 16 3 2.5z" fill="#F4EEE3"/><path d="M22.5 11.5L17.5 16l5 4.5L26 18c1.5-.9 1.5-3.1 0-4l-3.5-2.5z" fill="#F4EEE3" opacity="0.85"/><path d="M3 2.5l14.5 13.5L22.5 11.5 5 1.5c-.7-.4-1.5-.1-2 1z" fill="#F4EEE3" opacity="0.7"/><path d="M3 29.5l14.5-13.5 5 4.5L5 30.5c-.7.4-1.5.1-2-1z" fill="#F4EEE3" opacity="0.55"/></svg>
      <div>
        <span class="store-link-label">Get it on</span>
        <span class="store-link-name">Google Play</span>
      </div>
    </a>
  </div>
</section>

<footer class="colophon">
  <span>© MMXXVI · Kiki Gadgets Ltd.</span>
  <div class="colophon-links">
    <a href="index.html">Home</a>
    <a href="privacy-chore-scramble.html">Privacy Policy</a>
    <a href="mailto:support@kikigadgetsltd.com">Contact</a>
  </div>
  <span class="colophon-mark">Set in Fraunces &amp; Spectral</span>
</footer>

</body>
</html>
```

- [ ] **Step 2: If real Play Store content was captured in Task 1, edit hero deck and intro**

If Task 1's fetch succeeded, replace the placeholder hero deck and the two intro paragraphs with content based on the real Play Store description. Keep voice consistent with the editorial tone (calm, slightly literary, no marketing hype). The chapter list above (4 chapters) can be revised if the real listing reveals additional features.

If fallback content is in use, no edits needed.

- [ ] **Step 3: Visual verification**

```bash
open app-chore-scramble.html
```

Confirm:
- Hero with chore wheel mockup (terracotta/olive/amber/green quarters with "Spin" text in center and a triangle pointer at top)
- Four numbered chapters (I–IV)
- Pull quote
- Acquisition block with **only** Google Play button (no App Store)
- Privacy link goes to `privacy-chore-scramble.html`
- Mobile: layout stacks correctly

- [ ] **Step 4: Commit**

```bash
git add app-chore-scramble.html
git commit -m "Add Chore Scramble detail page"
```

---

## Task 6: Restyle `privacy-meal-lineup.html`

**Files:**
- Modify: `privacy-meal-lineup.html` (replace chrome; preserve all legal content verbatim)

**Important:** The text inside the `<h2>`, `<p>`, `<ul>`, `<li>` tags between the existing header and footer must be preserved word-for-word — only the surrounding HTML structure and a couple of class names change.

- [ ] **Step 1: Read the current file**

```bash
cat privacy-meal-lineup.html | head -50
```

Note the current structure:
- Header `<header>...</header>` with old logo/nav
- Main `<main class="privacy-page">...</main>` containing back-link, app-label, h1, effective date, all legal sections
- Footer `<footer>...</footer>` with old structure

- [ ] **Step 2: Replace the file in three precise edits**

**Edit A — Replace the `<header>` block (lines 11-26) with the new masthead and issue strip:**

Old (delete):
```html
  <header>
    <div class="header-inner">
      <div class="logo">
        <span class="logo-icon">⚙️</span>
        <div>
          <span class="logo-name">Kiki Gadgets</span>
          <span class="logo-tagline">Ltd.</span>
        </div>
      </div>
      <nav>
        <a href="index.html#apps">Apps</a>
        <a href="index.html#about">About</a>
        <a href="mailto:support@kikigadgetsltd.com">Contact</a>
      </nav>
    </div>
  </header>
```

New (insert):
```html
  <header class="masthead">
    <a href="index.html" class="masthead-back">← Kiki Gadgets</a>
    <div class="masthead-title">Kiki Gadgets, Vol. I</div>
    <div class="masthead-right">№ 01 · The Kitchen Issue</div>
  </header>

  <div class="issue">
    <span>Established <strong>MMXXVI</strong></span>
    <span>An Independent Studio</span>
    <span>Printed in code</span>
  </div>
```

**Edit B — Replace the `<main class="privacy-page">` open tag and the first three elements (back-link, app-label, h1) with the new privacy intro structure:**

Old (delete):
```html
  <main class="privacy-page">
    <a href="index.html" class="back-link">← Back to Home</a>

    <div class="app-label">Meal Lineup</div>
    <h1>Privacy Policy</h1>
    <p class="effective">Kiki Gadgets Ltd. &nbsp;|&nbsp; Effective Date: May 9, 2026</p>
```

New (insert):
```html
  <main class="privacy">
    <div class="privacy-kicker">Meal Lineup</div>
    <h1>Privacy Policy</h1>
    <p class="effective">Kiki Gadgets Ltd. · Effective Date: May 9, 2026</p>
```

Everything below (all the `<p>`, `<h2>`, `<ul>`, `<li>` content) stays exactly as written.

**Edit C — Replace the closing `</main>` block and the existing `<footer>` with the new colophon:**

Old (delete) — replace the trailing `<p style="...">© 2026...` block AND the old footer:
```html
    <hr />
    <p style="font-size:0.8rem; color:#94a3b8;">© 2026 Kiki Gadgets Ltd. All rights reserved.</p>
  </main>

  <footer>
    <div class="container footer-inner">
      <span>© 2026 Kiki Gadgets Ltd. All rights reserved.</span>
      <div class="footer-links">
        <a href="privacy-meal-lineup.html">Meal Lineup Privacy</a>
        <a href="privacy-chore-scramble.html">Chore Scramble Privacy</a>
        <a href="mailto:support@kikigadgetsltd.com">Contact</a>
      </div>
    </div>
  </footer>
```

New (insert):
```html
    <hr />
    <p style="font-size:11px; letter-spacing:0.2em; text-transform:uppercase; color:var(--ink-soft);">© 2026 Kiki Gadgets Ltd. All rights reserved.</p>
  </main>

  <footer class="colophon">
    <span>© MMXXVI · Kiki Gadgets Ltd.</span>
    <div class="colophon-links">
      <a href="index.html">Home</a>
      <a href="privacy-chore-scramble.html">Chore Scramble Privacy</a>
      <a href="mailto:support@kikigadgetsltd.com">Contact</a>
    </div>
    <span class="colophon-mark">Set in Fraunces &amp; Spectral</span>
  </footer>
```

- [ ] **Step 3: Add font preconnects + link to `<head>`**

Replace the existing `<head>` block with:
```html
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Meal Lineup — Privacy Policy | Kiki Gadgets Ltd.</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Fraunces:ital,opsz,wght,SOFT@0,9..144,300..900,30;0,9..144,300..900,100;1,9..144,400..700,50&family=Spectral:ital,wght@0,300;0,400;0,500;0,600;1,400&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="style.css" />
</head>
```

- [ ] **Step 4: Visual verification — confirm content is preserved**

```bash
open privacy-meal-lineup.html
```

Verify:
- Masthead + issue strip at top (matching app pages)
- Kicker "Meal Lineup" in italic terracotta
- "Privacy Policy" h1 in Fraunces
- Effective date strip with rules above & below
- **All 12 numbered sections still present**, in the same order: Information We Collect → How We Use → How We Share → Data Retention → Data Security → Children's Privacy → Your Rights → Third-Party Links → International Data Transfers → Changes → Copyright Policy (DMCA) → Contact Us
- DMCA registration number `DMCA-1071156` still appears in section 11
- Contact info still includes `support@kikigadgetsltd.com` and `www.kikigadgetsltd.com`
- Colophon footer

Search the file to confirm no legal text was accidentally lost:
```bash
grep -c "<h2>" privacy-meal-lineup.html
```
Expected: **12** (sections 1–12)

- [ ] **Step 5: Commit**

```bash
git add privacy-meal-lineup.html
git commit -m "Restyle Meal Lineup privacy page in editorial system"
```

---

## Task 7: Restyle `privacy-chore-scramble.html`

**Files:**
- Modify: `privacy-chore-scramble.html` (same wrapper transformation; preserve all legal content)

- [ ] **Step 1: Apply the same three edits as Task 6**

Use the same Edit A / B / C transformations from Task 6, with these differences:

- In Edit A's new masthead block, change `№ 01 · The Kitchen Issue` to `№ 02 · The Household Issue`
- In Edit B's new kicker, change `Meal Lineup` to `Chore Scramble`
- In Edit B's new effective date, change `May 9, 2026` to `March 11, 2026`
- In Edit C's colophon-links, the middle link should be `<a href="privacy-meal-lineup.html">Meal Lineup Privacy</a>` (not Chore Scramble — point to the other one)

All `<h2>`, `<p>`, `<ul>` content inside `<main>` stays exactly as written (sections 1–8 from the existing file: Information We Collect, How We Use, Data Storage and Third Parties, Children's Privacy, Data Security, Data Removal, Changes, Contact Us).

- [ ] **Step 2: Update `<head>` with font links** (same block as Task 6, but with title `Chore Scramble — Privacy Policy | Kiki Gadgets Ltd.`)

- [ ] **Step 3: Visual verification**

```bash
open privacy-chore-scramble.html
```

Verify:
- Masthead reads `№ 02 · The Household Issue`
- Kicker "Chore Scramble" + h1 "Privacy Policy"
- Effective date: March 11, 2026
- All 8 numbered sections preserved, including the explicit COPPA-relevant Children's Privacy section
- Colophon footer

Section count check:
```bash
grep -c "<h2>" privacy-chore-scramble.html
```
Expected: **8**

- [ ] **Step 4: Commit**

```bash
git add privacy-chore-scramble.html
git commit -m "Restyle Chore Scramble privacy page in editorial system"
```

---

## Task 8: Cross-page QA pass

**Files:**
- Possible touch-ups across any of the five pages

This task validates link integrity, responsive behavior, and visual cohesion across the redesigned site.

- [ ] **Step 1: Link map check**

For each page, click every internal link and confirm the destination:

| From | Link text | Should go to |
|---|---|---|
| index.html | "Read the issue →" (Meal Lineup spread) | app-meal-lineup.html |
| index.html | "Read the issue →" (Chore Scramble spread) | app-chore-scramble.html |
| index.html | "Privacy" (next to ML spread) | privacy-meal-lineup.html |
| index.html | "Privacy" (next to CS spread) | privacy-chore-scramble.html |
| app-meal-lineup.html | "← Kiki Gadgets" | index.html |
| app-meal-lineup.html | "App Store" button | apps.apple.com URL (new tab) |
| app-meal-lineup.html | "Google Play" button | play.google.com URL (new tab) |
| app-meal-lineup.html | "Privacy Policy" (in acquisition) | privacy-meal-lineup.html |
| app-chore-scramble.html | "← Kiki Gadgets" | index.html |
| app-chore-scramble.html | "Google Play" button | play.google.com URL (new tab) |
| app-chore-scramble.html | "Privacy Policy" (in acquisition) | privacy-chore-scramble.html |
| privacy-meal-lineup.html | "← Kiki Gadgets" masthead | index.html |
| privacy-chore-scramble.html | "← Kiki Gadgets" masthead | index.html |

If any link is broken, fix it inline and commit.

- [ ] **Step 2: Responsive check**

Open each of the five pages in a browser, then resize the window from desktop (>1200px) down to mobile (~375px). At each major breakpoint (1200, 900, 600, 400), confirm:
- No horizontal scrolling
- Text remains readable
- Phone mockups don't overflow
- Spreads/grids stack to single column at <900px
- Masthead "right" element hides on mobile (per `@media` rule)

If any layout breaks, add a fix to `style.css` and commit it as a follow-up.

- [ ] **Step 3: Lighthouse / accessibility quick check (optional but recommended)**

Run Lighthouse on `index.html` and `app-meal-lineup.html`:
```bash
# In Chrome DevTools, open Lighthouse panel, run on each page
```

Target scores: Accessibility ≥ 90, Best Practices ≥ 90. Common issues to fix:
- `<a>` tags with `aria-label` or visible text (all links should have it)
- Sufficient color contrast (terracotta on cream should pass; verify with DevTools)
- Heading hierarchy (one `<h1>` per page)

- [ ] **Step 4: Final commit if any fixes were made**

```bash
git add -A
git commit -m "QA pass: fix cross-page link / responsive issues"
```

- [ ] **Step 5: Update spec to reflect completed state**

Append a section to `docs/superpowers/specs/2026-05-15-editorial-site-redesign-design.md`:

```markdown
## Implementation Status

Implemented 2026-05-15. All five pages live with shared editorial system. Real app screenshots remain a follow-up task — currently using simulated UI mockups.
```

Commit:
```bash
git add docs/superpowers/specs/2026-05-15-editorial-site-redesign-design.md
git commit -m "Mark editorial redesign spec as implemented"
```

---

## Follow-up tasks (out of scope, captured for later)

- **Real app screenshots.** Replace the simulated phone-UI mockups with real screenshots from the App Store / Play Store. Download to `assets/screenshots/` and update `.phone-screen` blocks to use `<img>`. Plan: one task to download, one task to swap into each page.
- **`gh-pages` deploy verification.** The site uses a custom CNAME (`kikigadgetsltd.com`). After merging, confirm the live site renders the new design.
