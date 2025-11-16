# AI Coding Agent Instructions for Soins Agadir Center Website

## Project Overview
Single-page medical services website for a nursing & aesthetic clinic in Agadir. The site is multilingual (French/English/Arabic), features a filterable gallery, team carousel, and dark/light theme toggle. Repository: `sac.github.io` (GitHub Pages hosted).

## Architecture & Data Flow

### Language System (Core Pattern)
- **Single Source of Truth**: `translations.json` contains all text for en/fr/ar in nested structure
- **HTML Binding**: Elements use `data-key="translationKey"` attributes instead of hardcoded text
- **Runtime Loading**: JavaScript fetch in `<script>` tag loads translations.json and applies via `applyLanguage()` function
- **Storage**: User language preference persists in `localStorage.getItem("lang")` (default: "fr")
- **RTL Support**: Arabic automatically sets `dir="rtl"` and specific CSS rules handle layout reversal
- **When to Update**: All user-facing text must be added to translations.json first, then referenced via `data-key` in HTML

### Styling Architecture
- **CSS Variables** (`:root` scope): Primary, text, background, card-bg, hr colors
- **Theme System**: Dark/light toggled via `data-theme` attribute on `<body>`; localStorage saves preference
- **Bootstrap 4**: Base layout grid; extensive custom overrides in inline `<style>` tags
- **Inline Styles**: Nearly all custom CSS is in `<style>` within `<head>` (not separate .css files)—preserve this pattern when adding features
- **Responsive Breakpoints**: Template uses Bootstrap's col-lg-6, col-xl-6; custom media in templatemo-script.js via Slick carousel settings

### Components & Their Purpose

1. **Navigation Sidebar** (`#tm-sidebar`)
   - jQuery singlePageNav plugin for smooth section navigation
   - Mobile toggle button; closes on link click or outside click
   - Contains brand logo, nav links, copyright footer

2. **Gallery with Isotope** (`#gallery`)
   - Isotope.js filters by data-class (home-care, aesthetic, clinical, team)
   - Custom pagination (not Isotope's default); shows 12 items responsive to viewport
   - Magnific Popup lightbox for image viewing
   - imagesLoaded.js ensures layout recalculation after images load

3. **Team Carousel** (`#about .tm-carousel`)
   - Slick carousel with 9 slides (team members)
   - Responsive: 6 items (2500px) → 5 → 4 → 3 → 2 → 1 slide depending on width
   - Must recalculate height on language change (baked-in fix in applyLanguage())

4. **Parallax Sections** (`.tm-parallax`)
   - Parallax.js library; data-parallax="scroll" with data-image-src paths
   - Used for visual separation between home/gallery/about/contact sections

5. **Theme & Language Toggles** (`.tm-floating-icons`)
   - Fixed position top-right; circular icon buttons
   - Language menu dropdown appears on toggle click
   - Theme icon switches between moon/sun

## Critical Workflows & Commands

### Development/Testing (No build step)
- Open `index.html` directly in browser (no npm/build required)
- Changes are immediately visible; just refresh
- Translations require manual update to `translations.json` and browser refresh

### Adding New Content

**New Gallery Image**:
1. Save image to `img/gallery/tn/` (thumbnail) and `img/gallery/` (full-size)
2. Add `<figure>` element in gallery section with appropriate class filter
3. Add translation keys for Arabic title/caption in translations.json

**New Team Member**:
1. Add image to `img/staff[N].jpg`
2. Add carousel item in #about section with structure matching existing slides
3. Add team_[N]_name, team_[N]_desc, team_[N]_role keys to all three languages in translations.json

**New Navigation Link**:
1. Add `<li class="nav-item">` to #tm-main-nav
2. Add nav_[name] translation key to all languages
3. Section IDs must match href (e.g., `#gallery` → `id="gallery"`)

### Language-Specific Adjustments
- Arabic text automatically right-aligns via CSS rule `html[lang="ar"] ... { direction: rtl }`
- Icon positioning preserves left placement even in RTL (`.tm-post-icon order: 0`)
- Update all three language objects in translations.json simultaneously to avoid display gaps

## Project-Specific Patterns & Conventions

### Naming Conventions
- **CSS Classes**: Prefix `tm-` (e.g., `tm-gallery-item`, `tm-carousel`, `tm-text-primary`)
- **Translation Keys**: Hierarchical by section: `nav_*`, `home_p*`, `service_*`, `gallery_title`, `team_[N]_*`, `contact_*`, `about_*`
- **Data Attributes**: `data-filter` (gallery), `data-lang` (language option), `data-page` (pagination), `data-key` (translation)

### CSS Maintenance
- **Heavy Customization**: The `<style>` tag contains 300+ lines; additions should follow existing patterns
- **Card Styling**: `.tm-carousel-item`, `.tm-text-container`, `.media` all share `background: var(--card-bg)`
- **Spacing**: Margin/padding multiples of 5px; `.mb-5`, `.pb-4` for consistency
- **Responsive**: Test at 480px, 720px, 991px, 1260px, 1500px, 1920px breakpoints (from Slick config)

### JavaScript Patterns
- **jQuery 3.4.1** (not vanilla JS)
- **No Webpack/Modules**: All scripts concatenated in `<script>` tags
- **Event Handlers**: Use `.on("click", function)` pattern; delegation for dynamic content
- **Theme/Lang Persistence**: Always check localStorage and apply on page load
- **Slick Resize**: Window resize recalculates pagination and carousel layout

## Integration Points & External Dependencies

### Libraries Loaded (in order)
1. jQuery 3.4.1 → Slick → Parallax.js
2. imagesLoaded, Isotope (gallery filtering/paging)
3. jQuery Magnific Popup (lightbox)
4. Custom: templatemo-script.js

### Font & Icon Resources
- **Fonts**: Google Fonts (Kumbh Sans) loaded via link tag
- **Icons**: FontAwesome 5 (all.min.css + webfonts/) via `<i class="fas fa-*"></i>`
- **Social Icons**: Facebook, Twitter, Instagram, WhatsApp links hardcoded in HTML

### External Data
- **Map**: Google Maps embed (fixed URL in contact section; update src if address changes)
- **Phone Numbers**: Multiple formats (mobile/fix); keep tel: links functional
- **Instagram Link**: External link in nav and footer to @soins.agadir.center

## Critical Notes for Modifications

1. **Form Handling**: Contact form exists but has empty `action=""` attribute—backend not configured. Update action URL before enabling form submissions.

2. **Media Assets**: Gallery expects images at specific paths; missing images break layout. Always provide both thumbnail (tn/) and full-size versions.

3. **Carousel Height Bug Fix**: The `applyLanguage()` function includes special slick resize logic to prevent text overflow in carousel items during language switch. Preserve this when editing the function.

4. **RTL Complexity**: Arabic RTL support spans 50+ CSS rules and special media order property. Test any layout changes in all three languages before committing.

5. **No CSS Minification**: All inline styles are human-readable; maintain this for future editor accessibility.

## File Structure Reference
- `index.html` — Main markup; huge file (1900+ lines) with inline styles and translations script
- `translations.json` — All content; three nested language objects
- `js/templatemo-script.js` — Gallery pagination, carousel, navigation, magnific popup setup
- `css/templatemo-upright.css` — External stylesheet (mostly inactive; inline styles take precedence)
- `slick/` — Carousel CSS/JS (production build; don't edit)

## Success Checklist for AI Contributors
- [ ] Text changes: Updated translations.json for all three languages
- [ ] New elements: Added `data-key` attributes; tested in all languages
- [ ] Styling: Followed `tm-` prefix convention; tested at 480px and 1920px viewports
- [ ] Responsive: Verified carousel/gallery behavior on mobile and desktop
- [ ] Theme: Tested dark mode toggle; verified contrast and color variables
- [ ] RTL: Tested Arabic language; checked text alignment and icon placement
- [ ] Browser refresh: Confirmed translations load from localStorage on reload
