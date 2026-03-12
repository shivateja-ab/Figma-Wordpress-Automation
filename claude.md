# CLAUDE.md — Figma to Elementor Build Context

## Project Purpose
Convert Figma designs into fully responsive Elementor pages using MCP tools. 
Build locally on WordPress (LocalWP), then export templates to production.

## Available MCP Servers
- **elementor-mcp**: Creates and manages Elementor pages, containers, widgets, styles, and custom code on the local WordPress site.
- **figma** (or **framelink-figma**): Reads Figma design files — extracts layout, colors, typography, spacing, and component hierarchy.

## Build Rules — ALWAYS Follow These

### Structure
- ALWAYS use **Flexbox containers**. NEVER use legacy sections or columns.
- Use **nested containers** to match Figma's frame hierarchy.
- Use **percentage widths** (e.g., 50%, 33.33%) for columns. NEVER use fixed pixel widths for layout.
- Use `gap` property for spacing between elements. Avoid using margins between sibling elements.
- Maximum nesting depth: 4 levels. If deeper, flatten the structure.

### Responsive Design
- Build **desktop first**, then set overrides for tablet, then mobile.
- Desktop: default (1440px or 1200px content width)
- Tablet: 1024px breakpoint
- Mobile: 767px breakpoint
- On tablet: stack 3-column grids into 2 columns. Reduce font sizes by ~20%.
- On mobile: stack everything into 1 column. Reduce font sizes by ~35%. Make buttons full-width.
- Use `clamp()` for font sizes when possible: `clamp(28px, 4vw, 64px)`
- ALWAYS hide decorative background elements (blurred circles, floating shapes) on mobile.

### Typography
- Set global fonts in Elementor Site Settings before building pages.
- Headings: use Heading widget with proper H1-H6 tags (H1 for hero, H2 for sections, H3 for cards).
- Body text: use Text Editor widget.
- NEVER put all text in a single widget. Separate headings from paragraphs.

### Colors
- Set global colors in Elementor Site Settings before building pages.
- Reference global colors in widgets where possible instead of hardcoding hex values.

### Images
- All images must be uploaded to WordPress Media Library BEFORE building.
- Use Image widget for standalone images.
- Use container background image for hero/section backgrounds.
- Export decorative shapes (circles, stars, blobs) as SVG and upload to Media Library.
- Always set `max-width: 100%` and `height: auto` on images.

### Custom CSS
- Add page-level custom CSS for effects Elementor UI cannot handle.
- Use `selector` keyword to target the current element in Elementor's custom CSS.
- Common patterns to implement with CSS:
  - Text gradients: `background-clip: text` with `-webkit-text-fill-color: transparent`
  - Glassmorphism: `backdrop-filter: blur()` with semi-transparent background
  - Floating decorations: `position: absolute` on child, `position: relative` on parent container
  - Blurred background shapes: `border-radius: 50%` with `filter: blur(40px)` and low opacity
- For hover effects:
  - Buttons: `transform: scale(1.05)` with `transition: all 0.3s ease`
  - Cards: `transform: translateY(-5px)` with increased `box-shadow`

### Custom JavaScript
- Inject JS via Elementor's custom code tools (head/body snippets).
- Use `IntersectionObserver` for scroll-triggered animations (fade-in, slide-up).
- NEVER use jQuery. Use vanilla JavaScript only.
- Wrap all JS in `DOMContentLoaded` event listener.

### Decorative Elements
- Blurred circles/ellipses → CSS with absolute positioning (not image widgets)
- Stars/icons → upload SVG to media library, use Image widget or Icon widget
- Background gradients → container background with CSS gradient

## Build Workflow
1. Extract design context from Figma MCP (colors, fonts, layout, spacing).
2. Set up global colors and fonts in Elementor Site Settings.
3. Upload all images to WordPress Media Library.
4. Build page section by section, top to bottom.
5. After each section: set desktop styles, then tablet overrides, then mobile overrides.
6. Add page-level custom CSS for all hover effects and decorative elements.
7. Add custom JS for scroll animations last.

## When Building, Report Back
After building each section, tell me:
- What was built successfully
- What couldn't be matched exactly and why
- What needs manual adjustment in Elementor editor

## WordPress Site Info
- URL: http://my-site.local
- Admin: admin
- Elementor version: Free (no Pro widgets available)
- Available widgets: heading, text-editor, image, button, video, icon, spacer, divider, icon-box, html, shortcode
