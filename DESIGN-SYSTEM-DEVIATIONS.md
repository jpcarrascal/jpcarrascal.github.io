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

## Step 5: spacing and components

- **Spacing on a strict 8px grid instead of the plan's scale:** 4 / 8 / 16 / 24 / 32 / 48px (`--space-xs` to `--space-2xl`, in rem). The plan proposed 4 / 8 / 12 / 16 / 20 / 32 / 40.
- **All spacing tokenized in this step, not just buttons:** every margin, padding and gap in both stylesheets now uses the scale. Old values rounded to the nearest step, and ties rounded up:
  - 5 → 4; 6, 9, 10 → 8; 12, 15 → 16; 20 → 24; 30 → 32; 40 → 48
  - **Visible change:** the widespread 20px gaps and paddings (content padding, card padding, grid gaps) become 24px.
  - Exception, chosen by JP: the gap between project cards uses `--space-md` (16px) instead of rounding 20 up to 24.
- **Four components, not one Button.** The plan's "7 versions of one pattern" were four different things:
  - **Button** (`.button`, `.button--solid`): home links, the back button (37 pages) and the feb2026 CTA. One size: 8/16px padding.
  - **Tag** (`.tag`, `.is-active`): tag filter chips. The JS was updated for the new class names.
  - **Select** (`.select`): the language menu and the mobile tag filter, now with the same look (transparent, ink border).
  - **Badge** (`.badge`, was `.boxed`): static labels on `ux-approach`.
- **Components live in `style.css`**, since every page loads it. `project-template.css` keeps only layout wrappers (`.back-link`, `.cta-container`, `.language-selector`, tag filter layout).
- **Markup changes:** the back button and CTA styling moved from the wrapper `div` to the `<a>`. The whole button is now clickable; before, only the text was. Home links went from `<a><div class="link">` to `<a class="button">`.
- **Global link color is now `a { }` instead of `a:link, a:visited…`** (lower specificity), so component classes can override it.
- **Removed:** dead `.project-link` rules (unused since step 1), `.nounderline`, and `.link_hover`.
- **Visible changes:**
  - Home links: padding 5/9px → 8/16px (taller), and 4px gaps between them.
  - Back button and CTA: text goes from bold to regular weight, because the Button sets a regular weight. The CTA's padding goes from 10/20 to 8/16px.
  - Mobile tag select: transparent with an ink border, like the language menu.
  - Language menu: padding 5/10 → 4/8px.
  - Badges: square corners (were 4px radius), per the plan's radius 0.
- **Beyond the plan:** tokens were also added for border width, radius, motion (durations, hover opacity, card filters), layout widths, media heights and `z-index`, so that no raw design values remain. Only the media query breakpoints are still raw numbers, because CSS can't use variables in media queries.
- **Keyboard focus ring (not in the plan):** the old language menu removed the focus outline (`outline: none`), which hid keyboard focus. Buttons, tags, selects and project cards now share one focus ring, 2px in the border color with a 4px offset (`--focus-ring-width`, `--focus-ring-offset`). It appears only for keyboard focus (`:focus-visible`), not on mouse clicks.

## Step 6: inline styles

- **All 199 inline styles removed** from site pages, 6 of them inside JavaScript translation strings. The plan said "a few shared classes"; the replacements are:
  - Block classes: `.project-subtitle`, `.project-note`, `.project-text`, `.project-section`, `.project-media`, `.video`, `.badge-row`, `.media-narrow`.
  - Base rules, which need no class: images in `.project-content` are fluid, `hr` is styled globally, and consecutive citations in a publication box get `--space-md` between them.
- **Two media wrapper styles merged into `.project-media`** (`margin: 32px 0`): one had only a bottom margin, the other had top and bottom. The difference is mostly invisible, because vertical margins between stacked blocks collapse.
- **New tokens:** `--width-media-narrow` (620px, the two Count-Me-In figures that were deliberately shown smaller) and `--aspect-video` (560 / 400).
- **Small visible changes:**
  - Images inside figure tables are now capped at `--width-media` (720px), since the inline `max-width: 100%` no longer overrides `.figure-table img`. This affects at most ~30px, on wide images only.
  - Spacing rounded to the 8px grid: 20px → 32px around the Redmond photo on `ux-approach`; 15px → 16px between citations; 10px → 8px in the badge row; 40px → 48px around `ux-approach` dividers.
- **Docs updated so new pages don't reintroduce inline styles:** `projects/README.md` (class list, example, "no inline styles" rule) and the `project` skill. The README also listed a `.full-width-image` class that never existed; it was removed.
- **Untouched:** `print/`, `print-es/` and `apps/`, which are standalone pages outside the design system.
- **Added later:** `.media-standard`, which caps an image at `--width-media` (720px) so a standalone image lines up with the figures. It was first used for the Random Controller cover.

## Follow-up: consistency fixes (after step 6)

- **Canvas script tokenized:** `canvas-bg.js` no longer sets raw layering or timing values. The canvas uses `--z-background` (0) and lifted page elements use `--z-raised` (1), both new tokens. The show/hide fade uses `--duration-slow`. **Visible change:** the home background fades in over 0.3s instead of 0.5s.
- **Body text line height:** `.project-text` now uses `--line-height-relaxed` (1.5). Before, project paragraphs had no line height set, which rendered at about 1.2. **Visible change:** long paragraphs on every project page are more spaced out and easier to read.
- **Breakpoints consolidated to 900px and 600px**, documented in `tokens.css`. They can't be variables, because CSS doesn't allow `var()` in media queries.
  - **Tag filter:** it switches from chips to the dropdown at 900px instead of 768px. **Visible change:** between 769 and 900px you now see the dropdown.
  - **`max-device-width: 1204px` removed:** it was nearly dead code. Its width rule was always overridden, and it reset margins that were never set. Its one live rule, a 4px space under the home links, now applies at all sizes. **Visible change:** 4px more space above the home footer on desktop.
- **`--border-subtle` renamed to `--border-hidden`:** the border is the same color as the page, so resting tags read by their fill alone (1.16:1). That look is deliberate; the new name says what the token does, which is to keep resting tags the same size as the active one.
- **Home title on one line:**
  - Above 900px, the home column is `max(40%, --width-home-column)`, where `--width-home-column` is a new 28rem token. This fixes the wrap between 901 and 1030px.
  - Below 600px, the home title uses `--font-size-xl` (28.8px). It fits on one line on most phones but is tight at 360px.
- **Still open, no change:** the dark theme has no distinct values for `--surface-raised` and `--border-hidden`. That's fine until a dark page needs them.

## Follow-up: one details box per page

- **End-of-page sections consolidated:** 19 project pages now have exactly one `.publication-section` box holding all their links, publications and exhibitions. Before, there were up to 3 boxes, or links sat outside any box.
  - Pages that had more than one box: a-sense-of-surrounding, accidental, blesync, count-me-in, gamifying, intangible, reflex, spacebarman.
  - Pages with links outside the box, now moved in: ble-midi-glove, haptics-cylindrical, lockdown, magicscroll, momc-controller, opportunity-mapper, shape-emotions, whammyphone.
  - Pages whose box only needed its titles fixed: augmented-guitar, bluemo, syntheo.
- **Consistent titles:** every section title is an `h3` ending in a colon, in all three languages. The large `h2` "Links" titles are gone.
- **Standard order:** Links → Publication(s) → everything else (Exhibitions, Releases, Performances, Deployment). Some pages had this reversed.
- **Spacing inside the box:** titles after the first get `--space-lg` above them, and the last item has no bottom margin.
- **Class name kept:** `.publication-section`, by JP's choice, even though the box now holds more than publications.
- **Fixed on the way:**
  - opportunity-mapper had a stray `</div>` that closed the page container early.
  - a-sense-of-surrounding and intangible had no translations for their "Exhibition(s)" titles, so they stayed in English.
- **Not included:** hootbeat's "Demos" section is embedded videos, which is page content rather than links, so it stays where it is.
- **Docs:** the `project` skill and `projects/README.md` now describe the single-box structure.
- **Every item in the box is a bullet (later request):** under each title, all items (links, citations, exhibitions) are now `<li>` items in a single `<ul>`, even when there is only one. 20 sections on 16 pages changed:
  - Single `<p>` items each became a one-item list.
  - count-me-in's two citation paragraphs became one list.
  - random-controller's `<br>`-separated references became 8 list items.
  - Items with their own translation (`exhibition_text` on accidental and gamifying) kept their key, now on the `<li>`.
- **Box list styling uses tokens:** title bottom margin `--space-sm` (was the browser default), list indent `--space-lg` (was the browser's 40px), `--space-sm` between items, and items keep `--font-size-md` and `--line-height-relaxed`. **Visible change:** citations are no longer justified, since left-aligned reads better in a list.
- **Noted, not changed:** yo-soy-otro marks its "YouTube" and "IMDB" links for translation but has no translations for them. This is harmless: brand names stay as they are.

## Follow-up: vertical videos

- **New modifier:** `.video--vertical`, used with `.video`, for YouTube Shorts on hootbeat and chaotic-pedalboard. The plan's video format is landscape only.
- **New tokens:** `--aspect-video-vertical` (9 / 16) and `--width-video-vertical` (360px).
