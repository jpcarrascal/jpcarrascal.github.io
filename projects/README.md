# Project Template System

This directory contains a template system for creating consistent project pages on the JP Carrascal website.

## Files Overview

- `project-template.css` - Shared stylesheet containing all common project page styling
- Individual project directories (e.g., `intangible/`, `multitouchmixer/`) - Each contains project-specific content

## Template Structure

Each project page follows this standardized structure:

### HTML Template
```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en">
<head>
    <!-- Google Analytics -->
    <script async src="https://www.googletagmanager.com/gtag/js?id=UA-15242862-1"></script>
    <script>
      window.dataLayer = window.dataLayer || [];
      function gtag(){dataLayer.push(arguments);}
      gtag('js', new Date());
      gtag('config', 'UA-15242862-1');
    </script>
    
    <meta name="viewport" content="width=device-width,user-scalable=no" />
    <title>Your Project Title | JP Carrascal</title>
    
    <!-- Required stylesheets -->
    <link href="../../style.css" type="text/css" rel="stylesheet"/>
    <link href="https://fonts.googleapis.com/css?family=Lato:700|Lato" rel="stylesheet">
    <link href="../project-template.css" type="text/css" rel="stylesheet"/>
</head>
<body>
    <div id="flex-container">
        <div id="wrap">
            <div class="project-content">
                <!-- Language selector -->
                <div class="language-selector">
                    <select class="language-dropdown" id="languageSelector" onchange="switchLanguage(this.value)">
                        <option value="en">English</option>
                        <option value="es">Español</option>
                        <option value="ca">Català</option>
                    </select>
                </div>
                
                <!-- Project header -->
                <div class="project-header">
                    <h1 class="project-title" data-i18n="title">Your Project Title</h1>
                    <div class="project-authors">Author Names</div>
                </div>
                
                <!-- Your content goes here -->
                
            </div>
        </div>
    </div>
    
    <!-- Multilingual JavaScript -->
    <script>
        // Add your translations object here
        const translations = {
            en: {
                title: "Your Project Title",
                // Add more translations...
            },
            es: {
                title: "Título de tu Proyecto",
                // Add more translations...
            },
            ca: {
                title: "Títol del teu Projecte",
                // Add more translations...
            }
        };
        
        // Language switching functionality
        function switchLanguage(lang) {
            localStorage.setItem('preferredLanguage', lang);
            document.querySelectorAll('[data-i18n]').forEach(element => {
                const key = element.getAttribute('data-i18n');
                if (translations[lang] && translations[lang][key]) {
                    element.textContent = translations[lang][key];
                }
            });
        }
        
        // Load preferred language on page load
        document.addEventListener('DOMContentLoaded', function() {
            const savedLang = localStorage.getItem('preferredLanguage') || 'en';
            document.getElementById('languageSelector').value = savedLang;
            switchLanguage(savedLang);
        });
    </script>
</body>
</html>
```

## Available CSS Classes

The `project-template.css` provides these pre-styled components:

### Layout Classes
- `.project-content` - Main content container
- `.project-header` - Header section with title and authors
- `.project-title` - Main project title styling
- `.project-authors` - Author names styling

### Media Classes
- `.image-row` - Container for image galleries
- `.image-row-item` - Individual image container (50% width each)
- `.media-row` - Container for mixed media (video + image)
- `.media-row-item` - Individual media container (50% width each)
- `.full-width-image` - Full-width standalone images

### Interactive Elements
- `.language-selector` - Language dropdown container
- `.language-dropdown` - Styled dropdown for language selection

## Creating a New Project Page

1. **Create project directory**
   ```bash
   mkdir /projects/your-project-name
   cd /projects/your-project-name
   ```

2. **Create images directory**
   ```bash
   mkdir images
   ```

3. **Copy template HTML** (use the structure above)

4. **Customize content**
   - Update `<title>` tag
   - Replace project title and authors
   - Add your content using the available CSS classes
   - Create translations object for multilingual support

5. **Add images**
   - Place images in the `images/` directory
   - Use appropriate CSS classes for layout

## Content Layout Patterns

### Image Gallery (2 images per row)
```html
<div class="image-row">
    <div class="image-row-item">
        <a href="images/image1.jpg" target="_blank">
            <img src="images/image1.jpg" alt="Description" />
        </a>
    </div>
    <div class="image-row-item">
        <a href="images/image2.jpg" target="_blank">
            <img src="images/image2.jpg" alt="Description" />
        </a>
    </div>
</div>
```

### Mixed Media Row (Video + Image)
```html
<div class="media-row">
    <div class="media-row-item">
        <iframe src="https://player.vimeo.com/video/YOUR_VIDEO_ID?title=0&byline=0&portrait=0" allowfullscreen></iframe>
    </div>
    <div class="media-row-item">
        <a href="images/your-image.jpg" target="_blank">
            <img src="images/your-image.jpg" alt="Description" />
        </a>
    </div>
</div>
```

### Full-Width Image
```html
<div class="full-width-image">
    <a href="images/large-image.jpg" target="_blank">
        <img src="images/large-image.jpg" alt="Description" />
    </a>
</div>
```

### Text Content
```html
<div style="text-align: center; margin-bottom: 30px;" data-i18n="description">
    Your project description here.
</div>
```

## Multilingual Support

1. **Add data-i18n attributes** to translatable elements:
   ```html
   <p data-i18n="description">Default English text</p>
   ```

2. **Create translations object** in the JavaScript section:
   ```javascript
   const translations = {
       en: {
           description: "English description"
       },
       es: {
           description: "Descripción en español"
       },
       ca: {
           description: "Descripció en català"
       }
   };
   ```

3. **The language switching is automatic** - users' preferences are saved in localStorage

## Responsive Design

The template includes responsive breakpoints:
- Desktop: Full layout with side-by-side elements
- Mobile (≤900px): Stacked layout for better mobile viewing

## Examples

See existing projects for reference:
- `/projects/intangible/` - Art project with image gallery
- `/projects/multitouchmixer/` - Interface project with video and academic content

## Maintenance

- **Shared styles** are in `project-template.css` - modify once, affects all projects
- **Project-specific styles** can be added inline or in separate CSS files
- **Keep consistency** by using the provided CSS classes rather than custom styling