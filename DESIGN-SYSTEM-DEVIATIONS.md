# Design system: deviations from the plan

`DESIGN-SYSTEM-PLAN.md` is kept unchanged as the original brief. This file records every place where the implementation differs from it, and why. Add new entries under the step where they happen.

## Ground rules added during implementation

- **No hard-coded design values.** Colors (and later type sizes, spacing, radius, fonts) live only in `tokens.css`. Stylesheets, inline styles and scripts refer to tokens, never to raw values.
- **Color goes through semantic tokens; scales are used directly.** Color primitives (`--color-*`) are used only inside `tokens.css` to define the themes. Scale primitives (font sizes, weights, line heights, and later spacing) are used directly by components, since picking a step on the scale is the design decision.

## Corrections to the audit

- **Page count:** 39 pages use `project-template.css`: the portfolio, 36 project pages and 2 sub-pages (`ux-approach/dotnet-case-study`, `views/feb2026`). Add the home page and `canvas-test.html`, and that is 41 themed pages. The plan said 36 in total.
- **Contrast bug scope:** besides `multitouchmixer`, the light-on-light bug also hit `projects/views/feb2026/` (`.projects-intro` and the CTA button). `.figure figcaption` was a risk only: no page uses it.
- **Cause of the contrast bug:** the home page never loads `project-template.css`. The light-text rules there were not home rules leaking into project pages; they only ever applied to light pages.
- **Card hover filter:** the visible saturation change comes from `filter: contrast(1.2)` replacing the resting `filter: saturate(0)`, not from `saturate(1)`.

## Step 1: fix broken items

- **Rubik:** loaded at weights 400 and 700, together with Lato 400/700, with `display=swap`. Also updated in the `projects/README.md` template.
- **multitouchmixer:** besides the CSS fix, its back link was switched from `.project-link` to the standard `.back-link` markup used by every other page.
- **random-controller:** the plan said "fix or remove the link". A page was **created** instead, from JP's 2010 course report. Images are still pending.
- **Card filter:** only `filter: contrast(1.2)` was kept, so it looks the same as before.

## Step 2: tokens.css and hard-coded colors

- **Tag filter greys folded into the palette:** `#c9c9c9` became paper and `#8c8c8c` became ink. The plan only mentioned merging `#222`. Visible change: fainter tag borders, stronger hover border.
- **Dead code removed from `style.css`:** commented-out palettes and `@font-face` blocks.
- **Missed page:** `canvas-test.html` was not given the `tokens.css` link, which left its colors undefined. Fixed in step 3.

## Step 3: themes

- **Palette is 3 colors, not 4:** white was folded in. Tag buttons and the select now use paper-raised, and the active tag's text uses paper. Visible change on the tag filter only.
- **Two extra semantic tokens:** `--surface-inverse` (flipped panels, solid buttons, hover fills) and `--border-subtle` (faint control borders), on top of the plan's five.
- **Theme attribute on `<html>`:** pages without `data-theme` fall back to the dark theme.
- **Dark theme gaps:** `--surface-raised` and `--border-subtle` have no distinct value in the dark theme yet, because no dark page uses them.
- **Canvas background (`canvas-bg.js`):** it now reads `--surface-page` and `--text-primary` from the page. The hard-coded `#282828` (in `index.html`), the `"black"` default and the white line color were removed. Lines are now paper instead of pure white, which makes them a barely perceptible shade dimmer.
- **`ux-approach` dividers:** the inline `<hr>` styles now use `var(--border)`. They are still inline styles, to be handled in step 6.

## Step 4: type scale

- **6 steps instead of the plan's example:** 14 / 16 / 19.2 / 23 / 28.8 / 38.4px (`--font-size-xs` to `--font-size-2xl`, in rem). The plan's example 46px step was dropped. **Visible change:** project titles on desktop go from 46px to 38.4px, the same as the home title. Mobile was already 38.4px.
- **Fixed root size:** `html` no longer sets a font size (it was 1.2em on project pages and on narrow screens), so 1rem = the browser's base size everywhere and sizes no longer compound.
- **Body size is a theme token:** `--font-size-body` is 16px in the dark theme (home) and 19.2px in the light theme (project pages), the same as before. It sits with the color tokens because the theme attribute is what tells the two page types apart.
- **Fonts:** both families kept (`--font-display` Lato, `--font-body` Rubik). The plan left this open.
- **Line heights reduced to two tokens:** `--line-height-tight` 1.25 (was 1.2 / 1.25 / 1.3) and `--line-height-relaxed` 1.5 (was 1.5 / 1.6). **Visible change:** intro and description paragraphs that used 1.6 are slightly tighter.
- **Headings defined explicitly:** `h2` and `h3` now use scale steps instead of browser defaults. `h3` goes from 22.5px to 23px.
- **Rounding to the scale (small visible changes):**
  - captions and contact text: 17.3px → 16px
  - publication text: 18.2px → 19.2px
  - publication heading: 21.1px → 23px
  - language menu: 15.4px → 14px
  - `.project-description` (multitouchmixer only): 21.1px → 19.2px
  - home text on phones: 16.3px → 16px; home in a narrow desktop window: 19.2px → 16px
- **Dropped:** the tag button's 0.01em letter-spacing, which was invisible.
- **Not in the plan:** the `max-device-width: 1204px` media query no longer sets font sizes, but it still holds layout rules. It belongs to the breakpoint clean-up, which has no step yet.
