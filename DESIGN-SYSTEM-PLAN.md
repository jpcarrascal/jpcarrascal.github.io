# Design system consolidation plan: jpcarrascal.com

Brief for Claude Code. It summarizes an audit done in a separate Claude chat on 23 Nov 2026, covering 36 live pages and both stylesheets.

## Context

- The site is static HTML/CSS on GitHub Pages: `/` (home), `/projects/` (portfolio grid with tag filter) and 34 project pages under `/projects/<slug>/`.
- Stylesheets: `style.css` (global) and `projects/project-template.css` (portfolio and project pages, largely overrides of `style.css`).
- JP is learning design systems. Work in small, reviewable steps. Explain what each change does and why. Leave visual decisions to JP. Keep the site working after every step.

## Current look (keep it)

Monochrome, square corners (radius 0), wide-tracked uppercase Lato titles, Rubik body text. Home is light-on-dark; project pages are dark-on-light.

## Audit findings

### Broken
1. **Rubik is never loaded.** `html { font-family: 'Rubik' }` is set, but the only Google Fonts request is `Lato:700|Lato`. Visitors without Rubik installed see a fallback sans-serif.
2. **Invisible text on `/projects/multitouchmixer/`:** `.image-caption` and the "← Back" link render light on light (1:1 contrast). Cause: rules written for the dark home page (`color: var(--color-light)`) apply on the light project pages. Same risk in `.project-link a`, `.cta-container a`, `.projects-intro`, `.figure figcaption`.
3. **`/projects/random-controller/` returns 404** but is linked from the portfolio.
4. **`.project-card:hover`** sets `filter` three times (`saturate`, `brightness`, `contrast`). Only the last declaration applies.

### Colors
- Variables exist: `--color-dark #282828`, `--color-light #DEDEDE`, `--color-lighter #EEEEEE`.
- Bypassed by hard-coded values in the tag filter: `#222`, `#fff`, `#c9c9c9`, `#8c8c8c`. Note `#222` vs `#282828`: two near-identical darks.
- Dark/light inversion between home and project pages is done with overrides, not themes.

### Typography
- 26 size/weight/line-height combinations in use. Sizes are in `em` and compound (`html` is 1.2em on project pages and mobile), producing 15.36, 17.28, 18.24, 19.2, 21.12, 22.464, 23.04, 28.8 and 46.08px.
- Font name written as both `'lato'` and `'Lato'`. Commented-out `@font-face` blocks for Gill Sans and Montserrat remain.

### Buttons (7 versions of one pattern)
`.link`, `.project-link a`, `.cta-container a`, `.back-link`, `.tag-filter-button`, `.language-dropdown`, `.boxed`. Each has different padding (5/9, 6/10, 6/16, 8/16, 10/20…). `.boxed` has a 4px radius; everything else is square.

### Other
- 4–14 inline `style=""` attributes per project page, mostly repeated `text-align:center; margin-bottom:30px` and iframe sizing.
- Spacing uses 11 values: 4, 5, 6, 8, 9, 10, 12, 15, 20, 30, 40px.
- Breakpoints: 600, 768 and 900px, plus `max-device-width: 1204px` (deprecated).
- ID-based layout styles (`#flex-container`, `#wrap`, `#container`). Images and captions laid out with tables (`.figure-table`).

## Proposed system

### Tokens: new `tokens.css`, loaded first
- **Base colors:** ink `#282828`, paper `#DEDEDE`, paper-raised `#EEEEEE`, white `#FFFFFF`. Merge `#222` into ink.
- **Semantic colors:** `--surface-page`, `--surface-raised`, `--text-primary`, `--text-inverse`, `--border`.
- **Themes:** `[data-theme="dark"]` (home) and `[data-theme="light"]` (project pages) redefine the semantic colors. This replaces the override rules.
- **Fonts:** `--font-display` (Lato) and `--font-body` (Rubik, actually loaded). *JP to decide: keep both, or one family.*
- **Type scale (rem):** about 6 steps, e.g. 14 / 16 / 19 / 23 / 29 / 46px. *JP to approve.*
- **Spacing:** 4, 8, 12, 16, 20, 32, 40px. *JP to approve.*
- **Radius:** 0 everywhere.
- **Breakpoints:** 600px and 900px.

### Components (CSS classes)
Button (outline / solid / active), Tag (filter chip), Project card, Project header (title, authors, description), Figure (media + caption, without tables), Select (language menu).

## Migration order (one step per commit)

1. Fix the broken items: load Rubik, fix multitouchmixer contrast, fix or remove the random-controller link, clean up the card `filter`.
2. Add `tokens.css`. Replace hard-coded colors with variables.
3. Introduce `data-theme`. Remove the light/dark override rules.
4. Replace compounding `em` sizes with the rem type scale.
5. Consolidate the 7 buttons into one Button with variants.
6. Replace repeated inline styles with a few shared classes.
7. Later, optional: the 34 project pages repeat the same HTML. Consider a component-based static site tool (e.g. Astro) to turn them into one template.

## Working agreement
- Before each step: say which files change and what the visible effect will be (ideally none, except the bug fixes).
- After each step: JP checks the site locally before committing.
- Ask before deleting files or changing URLs.
