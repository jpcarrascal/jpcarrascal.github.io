# InTangible Project Page - Technical Specifications

## Overview
A multilingual static project page showcasing the InTangible artwork with image galleries, academic publication reference, and responsive design that maintains consistency with the main jpcarrascal.com site.

## Design Requirements

### Visual Style
- **Base styling**: Inherit from main site's `style.css` located at `../../style.css`
- **Background**: Solid color (`#DDD`) - no background image (override main site's background)
- **Typography**: Lato font family, consistent with main site
- **Color scheme**: 
  - Primary blue: `#137FFF` (for links and hover states)
  - Text: `#000` (titles), `#666` (subtitles/captions)
  - Interactive elements background: `rgba(0,0,0,0.4)`

### Layout Structure
- **Container**: Max-width 800px, centered, responsive
- **Layout flow**: Static document flow (not absolute positioning like main site)
- **Responsive**: Mobile-first approach with breakpoint at 900px

## Content Structure

### 1. Language Selector
- **Position**: Top-right of content area, inside main wrapper
- **Format**: Dropdown select element
- **Options**: English, Español, Català (English as default)
- **Styling**: Consistent with site's button styling
- **Functionality**: JavaScript-based language switching with localStorage persistence

### 2. Project Header
- **Title**: Multi-line project title (translatable)
- **Authors**: "JP Carrascal, Ina Ghita" (static across languages)
- **Typography**: Large title with letter-spacing, centered

### 3. Image Gallery - Top Row
- **Images**: `intangible01.jpg`, `intangible02.jpg`, `intangible03.jpg`
- **Layout**: Horizontal row, equal width distribution
- **Sizing**: Fixed height (200px), `object-fit: contain` (no cropping)
- **Styling**: No border radius, clickable to open full-size in new tab
- **Responsive**: Stack vertically on mobile (<900px)

### 4. Project Descriptions
- **Format**: Two separate text blocks separated by system diagram
- **Content**: Academic/artistic description (translatable)
- **Typography**: Justified text, readable line-height (1.6em)

### 5. System Diagram
- **Image**: `intangible-diagram.png`
- **Size**: 50% width, centered
- **Caption**: "System architecture" (translatable)
- **Functionality**: Clickable to view full-size

### 6. Publication Reference
- **Position**: After main content, before navigation
- **Styling**: Subtle background box with rounded corners
- **Content**: Academic citation with clickable DOI link
- **Format**: 
  ```
  Publication: (translatable label)
  Juan Pablo Carrascal and Ina Ghita. 2023. InTangible: A Reflection On Digital vs. Physical Co-Ownership. In Proceedings of the Seventeenth International Conference on Tangible, Embedded, and Embodied Interaction (TEI '23). Association for Computing Machinery, New York, NY, USA, Article 61, 1–2. https://doi.org/10.1145/3569009.3576187
  ```

### 7. Navigation & Contact
- **Back button**: JavaScript `history.back()` functionality
- **Contact**: "Contact: jp [at] jpcarrascal [dot] com" (translatable)

## Technical Specifications

### Multi-language Implementation
- **Storage method**: Embedded JavaScript object in HTML
- **Languages**: English (en) - default, Spanish (es), Catalan (ca)
- **Implementation**: Data attributes (`data-i18n`) + JavaScript switcher
- **Persistence**: localStorage for user preference
- **Accessibility**: Document language attribute updates

### Translation Keys
```javascript
{
  title: "Project title",
  description1: "First description paragraph", 
  description2: "Second description paragraph",
  diagram_caption: "System diagram caption",
  publication_label: "Publication section header",
  back_button: "Back navigation text",
  contact: "Contact information"
}
```

### Image Requirements
- **Format**: JPG/PNG
- **Location**: `images/` subdirectory
- **Files needed**:
  - `intangible01.jpg`, `intangible02.jpg`, `intangible03.jpg` (gallery)
  - `intangible-diagram.png` (system architecture)
- **Optimization**: Web-optimized for fast loading

### CSS Architecture
- **Base**: Import main site stylesheet
- **Overrides**: Remove background image, adjust layout for static flow
- **Custom styles**: 
  - Language selector styling
  - Image gallery layouts (row and individual)
  - Publication reference box
  - Mobile responsive adjustments

### JavaScript Functionality
- **Language switching**: Dynamic content updates without page reload
- **Preference storage**: localStorage integration
- **Initialization**: Auto-detect saved language on page load
- **Error handling**: Fallback to English if translation missing

## File Structure
```
projects/intangible/
├── index.html (main page)
├── images/
│   ├── intangible01.jpg
│   ├── intangible02.jpg  
│   ├── intangible03.jpg
│   ├── intangible-diagram.png
│   └── photo.jpg (legacy, may be unused)
└── PROJECT_SPECS.md (this file)
```

## Browser Compatibility
- **Modern browsers**: Chrome, Firefox, Safari, Edge (last 2 versions)
- **Mobile**: iOS Safari, Chrome Mobile
- **Features**: ES6+ JavaScript, CSS Flexbox, localStorage API

## Performance Requirements
- **Load time**: <3 seconds on 3G connection
- **Image optimization**: Images should be web-optimized
- **JavaScript**: Minimal, efficient DOM manipulation
- **No external dependencies**: Self-contained implementation

## Accessibility Requirements
- **Language attributes**: Proper `lang` attribute updates
- **Alt text**: Descriptive alt text for all images
- **Keyboard navigation**: Dropdown and links keyboard accessible
- **Color contrast**: Meet WCAG AA standards
- **Screen readers**: Semantic HTML structure

## Content Guidelines

### English (Default)
- Academic tone appropriate for art/technology research
- Technical precision for research context
- International English conventions
- Technical terms: NFT, co-ownership, mixed media

### Spanish Translation
- Maintain academic register
- Technical terms: NFT, co-propiedad, técnica mixta

### Catalan Translation  
- Regional linguistic standards
- Consistent with Catalonian academic writing
- Proper technical terminology adaptation

## Quality Assurance Checklist
- [ ] All images load correctly and are clickable
- [ ] Language switching works for all content
- [ ] Mobile responsive design functions properly
- [ ] Publication DOI link works
- [ ] Back button functions correctly
- [ ] Language preference persists across sessions
- [ ] All translations are accurate and contextually appropriate
- [ ] Loading performance meets requirements
- [ ] Cross-browser compatibility verified

## Maintenance Notes
- Translation updates require editing JavaScript object in HTML
- New images should follow naming convention: `intangible##.jpg`
- Publication reference may need updates for new citations
- Test language switching after any HTML structure changes

---

**Document Version**: 1.0  
**Last Updated**: October 11, 2025  
**Created by**: GitHub Copilot Assistant  
**Project**: jpcarrascal.github.io/projects/intangible