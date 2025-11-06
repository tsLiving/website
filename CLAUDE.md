# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

TSLiving website built with Eleventy (11ty) static site generator, TailwindCSS, and Alpine.js. The site is deployed to Netlify. Based on the 11ta template by Shane Robinson.

## Development Commands

### Development
```bash
npm run dev
# Runs clean, then starts both Tailwind and Eleventy in watch mode
# - Tailwind watches src/assets/css/tailwind.css and outputs to dist/css/styles.css
# - Eleventy serves the site with live reload on http://localhost:8080
```

### Production Build
```bash
npm run build
# Sets NODE_ENV=production and ELEVENTY_PRODUCTION=true
# Runs clean, then builds both Tailwind (minified) and Eleventy
```

### Individual Tasks
```bash
npm run clean                # Remove dist directory
npm run dev:tailwind        # Watch and compile Tailwind CSS
npm run dev:eleventy        # Start Eleventy dev server
npm run build:tailwind      # Build minified Tailwind CSS
npm run build:eleventy      # Build Eleventy for production
npm run debug               # Run Eleventy with DEBUG=* for troubleshooting
```

## Architecture

### Directory Structure

```
src/
├── _data/                  # Global data files
│   ├── meta.js            # Site metadata, SEO, contact info
│   ├── structure.js       # UI configuration (layouts, styling options)
│   ├── colors.js          # Color theme definitions
│   └── build.js           # Build-time data
├── _includes/
│   ├── layouts/           # Page layouts (base, page, post, author)
│   ├── components/        # Reusable UI components
│   │   ├── nav.njk        # Navigation bar
│   │   ├── hero-*.njk     # Various hero section types
│   │   ├── postslist*.njk # Blog post listing layouts
│   │   └── ...
│   └── partials/          # Partial templates (head, footer)
├── assets/
│   ├── css/               # Tailwind CSS source
│   ├── svg/               # SVG assets (including logo)
│   ├── images/            # Image assets
│   └── video/             # Video assets
├── posts/                 # Blog posts (markdown)
├── admin/                 # CMS admin interface
└── *.md, *.njk           # Top-level pages

utils/                     # Eleventy utilities
├── filters.js             # Template filters
├── shortcodes.js          # Template shortcodes (youtube, markdown)
├── paired-shortcodes.js   # Paired shortcodes
└── collections.js         # Collection definitions

dist/                      # Build output (gitignored)
```

### Key Configuration Files

- **`.eleventy.js`**: Main Eleventy configuration
  - Registers plugins (RSS, navigation, syntax highlighting, SVG sprite, favicons)
  - Loads filters, shortcodes, and paired shortcodes from utils/
  - Defines collections (posts, tags)
  - Sets up watch targets and passthrough copy
  - Configures markdown-it with emoji support
  - Sets template formats: html, njk, md

- **`tailwind.config.js`**: TailwindCSS configuration
  - Custom color palette: brown (primary), silver, charcoal
  - Extended heights for hero sections
  - Content paths include all Nunjucks, HTML, MD files and data files

- **`src/_data/structure.js`**: Global UI configuration
  - Page width and spacing (`g_pageWidth`)
  - Post list styles (postslist, postslistblocks, postslistthumbs, postslistcards)
  - Feature toggles (tag pills, author blocks, social icons, excerpts)
  - Pagination alignment

- **`src/_data/meta.js`**: Site metadata
  - Site name, description, URL
  - SEO and social media tags
  - Contact information
  - Author details

### Template System

**Layouts** (in `src/_includes/layouts/`):
- `base.njk`: Base HTML structure with head, nav, footer
- `page.njk`: Standard page layout with optional hero sections
- `post.njk`: Blog post layout with metadata, tags, social sharing
- `author.njk`: Author profile layout

**Hero Section Types** (configured via frontmatter `hero:` field):
- `graphic`: Hero with background image
- `split`: Split layout with image and text
- `carousel`: Image carousel
- `video`: Video background
- `text-overlay`: Text over image/video

**Post List Styles** (configurable in `structure.js`):
- `postslist`: Simple text link list
- `postslistblocks`: Block layout with metadata
- `postslistthumbs`: Blocks with thumbnail images
- `postslistcards`: Card-based layout

### Plugin Integration

**SVG Sprite Plugin** (`eleventy-plugin-svg-sprite`):
- Processes SVGs from `src/assets/svg/`
- Creates sprite with `fill-current` class by default
- SVGs are copied to dist and available as sprite symbols

**Favicons Plugin** (`eleventy-plugin-gen-favicons`):
- Generates favicon variants
- Outputs to `dist/` directory

**Navigation Plugin** (`@11ty/eleventy-navigation`):
- Pages use `eleventyNavigation` frontmatter
- Navigation filtered by `navKey("main")`
- Used in `nav.njk` component

### Collections and Content

**Posts Collection**:
- Source: `src/posts/*.md`
- Draft posts (frontmatter `draft: true`) excluded in production
- All posts visible in development for preview

**Tags Collection**:
- Auto-generated from post frontmatter
- Filtered to exclude: 'authors', 'pages', 'post'
- Powers tag-based navigation

### Styling System

**Color Theming**:
- Custom colors defined in `tailwind.config.js`
- Color configurations in `src/_data/colors.js`
- Components use fallback pattern: `colors.navCustom.* or colors.navDefault.*`

**Page Width Control**:
- Consistent spacing via `structure.g_pageWidth`
- Applied to nav, page content for uniform margins

## Important Development Notes

### Logo Integration
- The new logo file is at: `src/assets/svg/logo-tim-cleanup.svg`
- Current placeholders to replace:
  - `logo-placeholder.svg` in `src/_data/meta.js` (siteImage)
  - `logo-placeholder.svg` in `src/_includes/components/nav.njk` (lines 15, 23)
  - `logo-placeholder.svg` in `src/index.md` (SEO image)

### Working with Assets
- SVGs placed in `src/assets/svg/` are:
  1. Copied to `dist/assets/svg/` (passthrough)
  2. Processed into SVG sprite (if used via sprite plugin)
- Images in `src/assets/images/` are copied to dist
- Always reference assets from `/assets/` path (dist output path)

### Environment Variables
- `ELEVENTY_ENV=production`: Enables production mode (filters draft posts)
- `NODE_ENV=production`: Used for production builds
- `URL`: Sets `meta.siteURL` (defaults to https://tsliving.de)

### Adding New Pages
1. Create `.md` or `.njk` file in `src/`
2. Add frontmatter with layout, title, excerpt
3. Optionally add `eleventyNavigation` for nav menu inclusion
4. Choose hero type with `hero:` and configure `heroSettings:`

### Customizing Components
- Duplicate existing component in `src/_includes/components/`
- Modify as needed
- Reference in layouts or page templates
- For post list styles: add new component, update `structure.js` options

### Brand Colors
- Primary: `brown` (#5A3A1E - dark brown)
- Highlight: `brown-light` (#B67B4C - copper brown)
- Secondary: `silver` (#E5E5E5), `charcoal` (#2E2E2E)
