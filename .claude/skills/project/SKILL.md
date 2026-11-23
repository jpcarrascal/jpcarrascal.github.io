---
name: project
description: Create a new project entry or edit an existing one in this portfolio's /projects section. Scaffolds the project HTML page from the repo's existing templates, writes EN/ES/CA translations, and wires the project into the portfolio grid and tag filter. Use whenever the user wants to add a project to the portfolio, or add/change fields (images, links, publications, exhibitions, translations) on an existing project page.
---

# Project entries

This repo's `/projects` section is a set of static, per-project pages under `projects/<slug>/index.html`, listed as cards on `projects/index.html`. Every project page is self-contained and trilingual (English/Spanish/Catalan), switched client-side via a `data-i18n` + `translations` JS object — there are no separate `/es/` or `/ca/` files or folders.

Before doing anything, read at least one existing project as a live reference — `projects/accidental/index.html` (has subtitle, gallery, notes, links, exhibition) and `projects/shape-emotions/index.html` (minimal: header, subtitle, one image, description, links, publication) cover most cases between them. Copy structure and boilerplate from real files rather than inventing it — the GA snippet, `<head>` block, language-selector markup, and `switchLanguage`/`DOMContentLoaded` script are byte-for-byte identical across every project page and must stay that way.

## Determine mode

- **Create**: the user wants a new project added. Slug doesn't exist yet under `projects/`.
- **Edit**: the user wants to change/extend an existing project (add a gallery image, add a publication, fix a translation, add a link, etc). Find the existing `projects/<slug>/index.html`, read it fully, and make the minimal change requested — don't restructure unrelated parts of the page.

## Create flow

### 1. Ask for the required fields (if not already given)

- **Title** — proper name of the project. Stays identical across all three languages (these are proper nouns/titles, per convention observed in every existing project).
- **Authors** — as they should appear, e.g. `JP Carrascal`, `JP Carrascal, Tom DeMajo`, or a stage name. Not translated, not wrapped in `data-i18n`.
- **Year** (or year range, e.g. `2010 - Present`) — appended to authors in parens: `<div class="project-authors">{authors} ({year})</div>`, and to the card title on the portfolio grid: `<h3>{Title} ({year})</h3>`.
- **Subtitle** — one short line, centered, right under the header (`data-i18n="subtitle"`). Optional in the template system generally, but ask for it since the user wants it treated as required for new entries.
- **Description** — the main English body text. Can be one or more `<p>` (wrap in `<p>` tags only if multi-paragraph; a single paragraph can be plain text inside the div). Rendered via `data-i18n="description"` (or `description_main` if the project also has a separate `notes_content` block — follow `accidental`'s split in that case).
- **Cover image** — ask what filename to use (default suggestion: `main.jpg`, matching most existing projects). Don't require the file to exist yet — wire up the `<img>` reference and tell the user where to drop it at the end (see "Image handling" below).

### 2. Ask for optional fields — don't assume absence, ask explicitly

- **Additional gallery images** — a 3-photo (or fewer) row like `accidental`'s Sant Agustí figure, using `.image-row` / `.image-row-item` inside a `.figure-table` with a `.figure-caption` (`data-i18n`'d if it's a short original sentence; leave untranslated if it's just a venue name/date).
- **Links, Publication(s), Exhibition(s)** all go in **one** box at the end of the page, `<div class="publication-section">`, never several boxes and never outside the box. Inside it, each section has an `<h3 data-i18n="...">` title ending in a colon (in all three languages), in this order:
  1. **Links:** `<h3 data-i18n="links_label">Links:</h3>` then `<ul><li><a ...>Label</a></li></ul>`.
  2. **Publication(s):** `<h3 data-i18n="publication_label">Publication:</h3><ul><li>Full citation with a DOI/link</li></ul>` (one `<li>` per citation). The citation text itself is **never translated**; only the label (`Publication:`/`Publicación:`/`Publicació:`) changes.
  3. **Anything else** (Exhibition(s), Releases, Performances, Deployment): `<h3 data-i18n="exhibition_label">Exhibition(s):</h3>` followed by a `<ul>`: either one `<li data-i18n="exhibition_text">` (translate it if it's an original short sentence) or several `<li>` venue/date entries (leave list items untranslated, as in `intangible`).
  **Every item under a title is an `<li>` in one `<ul>`, even if there is only one item** (no `<p>`, no `<br>`-separated lines). Omit sections the project doesn't have; omit the whole box if it has none. `spacebarman` and `count-me-in` are good multi-section references.

### 3. Translate to Spanish and Catalan

Write natural, idiomatic ES/CA translations yourself — don't ask the user to supply them unless they want to review/correct. Use proper accents and diacritics (é, í, ó, ú, ñ, à, è, ï, ç, l·l, etc.) — match the quality bar of most existing projects (e.g. `a-sense-of-surrounding`, `count-me-in`, `opportunity-mapper`), not `accidental`'s translations, which are an outlier that dropped accents and shouldn't be used as the style reference for translation quality (only for structure).

Keep untranslated: the project title, author names, bibliographic citations, and venue/date lists (see above).

### 4. Assemble the page

Build `projects/<slug>/index.html` (slug = kebab-case of the title unless the user specifies otherwise) following this skeleton, adapted from `shape-emotions`/`accidental`:

- Identical `<head>` boilerplate (GA snippet, viewport/charset meta, `<title>{Title} | JP Carrascal</title>`, `../../style.css`, Lato font, `../project-template.css`).
- Identical language-selector `<select>` block.
- `.project-header` with `h1.project-title[data-i18n=title]` and `.project-authors`.
- Subtitle: `<div class="project-subtitle" data-i18n="subtitle">`.
- Cover image inside `<div class="project-media">` (no inline sizing: images in `.project-content` are already fluid).
- Description: `<div class="project-text" data-i18n="description">`.
- **Never use inline `style=""` attributes or raw design values.** Use the classes listed in `projects/README.md` (`.project-section`, `.project-media`, `.video`, etc.); if a new style is needed, add a class in `project-template.css` that uses tokens from `tokens.css`.
- Any optional blocks requested (gallery row, links, publication, exhibition), in that rough order — but match whatever order makes sense for the actual content.
- `.back-link` with `data-i18n=back_button` (`← Back` / `← Atrás` / `← Enrere`).
- The `<script>` block: a `translations` object keyed `en`/`es`/`ca`, each containing every `data-i18n` key used on the page. `switchLanguage()` must `innerHTML` any key whose content includes HTML markup (e.g. `<em>`, `<br>`, links) and `textContent` everything else — check the `if (key === '...')` condition in the copied script and extend it to cover every HTML-bearing key you added. The `DOMContentLoaded` handler and the rest of `switchLanguage` are boilerplate — copy verbatim.

### 5. Wire it into the portfolio grid

In `projects/index.html`:

- Add a `.project-card` inside `.projects-grid`:
  ```html
  <a class="project-card" href="{slug}/">
      <img src="{slug}/images/{cover-filename}" alt="{Title}">
      <div class="project-info">
          <h3>{Title} ({year})</h3>
          <p>{short one-line English blurb for the card — not the full description}</p>
      </div>
  </a>
  ```
- Placement: cards are roughly newest-first. Insert the new card next to others from the same/adjacent year rather than at a fixed position — check the surrounding years and use judgment.
- Add an entry to `const projectTagsById = {...}` keyed by slug, e.g. `"{slug}": ["tag1", "tag2"],`. For tags: show the user the existing tag vocabulary already in that dict and ask them to pick from it, but allow introducing a genuinely new tag if none fit — don't silently invent tags without asking.

Do **not** touch `print/projects-config.json` / `print-es/projects-config.json` unless the user asks — that's a separate, curated "selected works" list, not a mirror of every project.

### 6. Image handling

Don't wait for image files to exist before finishing the page — wire up all `<img src="...">` references to the filename(s) agreed with the user (defaulting to `images/main.jpg` for the cover if the user has no preference), and end the turn with a clear, specific note of exactly which path(s) the user needs to drop files at, e.g.:

> Drop the cover photo at `projects/{slug}/images/{cover-filename}` — that's what both the project page and its portfolio card reference.

If the user says images are ready and gives a path, verify the file actually exists (`ls`) before referencing it, and use its real filename rather than renaming/assuming `main.jpg`.

## Edit flow

1. Read the full existing `projects/<slug>/index.html` before changing anything.
2. Make the requested change (new gallery image, new link, new publication, exhibition update, wording fix, translation fix, etc.), keeping it scoped to what was asked — don't refactor the rest of the page.
3. **Any change to translated text must be applied to all three language blocks** (`en`, `es`, `ca`) in the `translations` object, not just the inline English markup in the body. If the user edited the inline English text directly (visible as a diff before your turn), treat that as the new source of truth for English and update `es`/`ca` to match, translating any new/changed content yourself.
4. If the change affects the portfolio card (title, year, cover image path) or tags, update `projects/index.html` (`.project-card` block and/or `projectTagsById`) to match.
5. If adding an image, confirm the file exists in `projects/<slug>/images/` (`ls`); if it doesn't yet, wire up the reference anyway and tell the user where to drop it, per "Image handling" above.

## General conventions worth re-checking against a live file if unsure

- Card blurbs on the grid are short (one line) and always English-only — the grid itself isn't translated, only individual project pages are.
- `project-authors` and `project-authors-noitalic` are never wrapped in `data-i18n` — author names/years don't get translated.
- File/folder naming is kebab-case matching the project slug.
- Don't add a subtitle, gallery, links, publication, or exhibition section the user didn't ask for — many existing projects have only a subset of these.
