# CLAUDE.md — Figma to Elementor Build Context

## Project Purpose
Convert Figma designs into pixel-perfect, fully responsive Elementor pages using MCP tools.
Build locally on WordPress (LocalWP), then export templates to production.

## Available MCP Servers
- **elementor-mcp**: Creates and manages Elementor pages, containers, widgets, styles, and custom code on the local WordPress site.
- **figma** (or **framelink-figma**): Reads Figma design files — extracts layout, colors, typography, spacing, and component hierarchy.

---

## CRITICAL RULES — NEVER BREAK THESE

### Pixel-Perfect Accuracy
- The output MUST match the Figma design exactly. Every pixel matters.
- Extract ALL design specs from Figma down to the smallest detail: exact hex colors, exact font sizes, exact font weights, exact line heights, exact letter spacing, exact padding, exact margins, exact border-radius, exact shadows, exact gradients, exact opacity values.
- Do NOT add any styles, elements, colors, shadows, borders, backgrounds, or decorations that are not explicitly present in the Figma design.
- Do NOT improvise or "enhance" the design. If it's not in Figma, it does not exist.
- Do NOT add hover effects, animations, or transitions unless I explicitly ask for them.
- If something is ambiguous in the Figma file, ASK ME before guessing.

### Figma Extraction
- I will provide a Figma URL with a specific **node-id**. Target ONLY that node.
- Use the Figma MCP to pull the complete design specs for that node and all its children.
- Extract every detail: container dimensions, padding, margin, gap, background colors, background gradients, border, border-radius, box-shadow, opacity, blend modes, font family, font size, font weight, font style, line height, letter spacing, text alignment, text color, text transform, image dimensions, image border-radius, icon sizes.
- If the Figma MCP returns incomplete data for any element, tell me what is missing and ask me to provide it.

### No Absolute Positioning
- NEVER use `position: absolute` or `position: fixed` or `position: sticky` on any element.
- ALL layout must be achieved using Elementor Flexbox containers with `flex-direction`, `justify-content`, `align-items`, `gap`, `padding`, and `margin`.
- Decorative elements (blurred shapes, floating icons) must be built using containers with background styles, padding, and margin — NOT absolute positioning.
- If a design element seems to require absolute positioning, ask me how to handle it instead of guessing.

### Pure WordPress Elementor Widgets Only
- Build EVERYTHING using native Elementor widgets. No custom HTML widget. No shortcode widget. No raw HTML or code injection for layout or content.
- Allowed widgets: Heading, Text Editor, Image, Button, Icon, Icon Box, Image Box, Star Rating, Divider, Spacer, Video, Google Maps, Progress Bar, Testimonial, Tabs, Accordion, Toggle, Social Icons, Alert, Counter, Image Carousel.
- For text content: use Heading widget (H1–H6) or Text Editor widget.
- For images: use Image widget.
- For buttons: use Button widget.
- For icons: use Icon widget or Icon Box widget.
- For spacing: use Spacer widget or container padding/gap.
- The ONLY place code is allowed is in Custom CSS and Custom JS fields — NEVER as content widgets.

### Custom CSS
- Use Elementor's Custom CSS field (page-level and section-level) for all styling that Elementor's UI cannot achieve.
- Write all custom CSS using Elementor's `selector` keyword to target elements.
- After building each page, provide me with a COMPLETE compiled document containing:
  - ALL custom CSS used, organized by section with clear comments
  - The CSS class names applied to each container/widget
  - Instructions on which section/widget each CSS block belongs to
- This CSS document is for copy-pasting into production. Make it clean, commented, and production-ready.

### Custom JavaScript
- Use vanilla JavaScript only. NEVER use jQuery.
- Only add JS if I explicitly request interactions (scroll animations, toggles, etc.).
- Wrap all JS in `document.addEventListener('DOMContentLoaded', function() { ... })`.
- After building, provide me with a COMPLETE compiled document containing:
  - ALL custom JS used, with comments explaining what each block does
  - Instructions on where to paste it in production (which section or page-level)
- This JS document is for copy-pasting into production. Make it clean, commented, and production-ready.

### Responsive Design
- The Figma design is for **laptop/desktop only**. I will NOT provide tablet or mobile designs.
- You MUST generate tablet and mobile layouts based on the desktop design following these rules:
- Build **desktop first** matching Figma exactly, then create tablet and mobile overrides.

**Desktop (default):**
- Match Figma design pixel-for-pixel. No changes.

**Tablet (1024px and below):**
- 3-column layouts → 2 columns
- Side-by-side layouts with content + image → stack vertically (content on top, image below)
- Reduce all heading font sizes by 20% from desktop
- Reduce body text by 10% from desktop
- Reduce container padding by 25% from desktop
- Maintain all colors, borders, shadows, and border-radius exactly as desktop
- Navigation: if horizontal nav exists, keep it but reduce spacing

**Mobile (767px and below):**
- ALL multi-column layouts → single column
- Reduce all heading font sizes by 35% from desktop
- Reduce body text by 15% from desktop
- Reduce container padding by 50% from desktop
- Buttons: full width (`width: 100%`)
- Images: full width with `max-width: 100%` and `height: auto`
- Hide purely decorative elements that add no content value
- Minimum tap target size: 44px for all interactive elements
- Maintain all colors, borders, shadows, and border-radius exactly as desktop

---

## Build Workflow

### Step 1: Extract from Figma
When I give you a Figma URL with a node-id:
1. Use Figma MCP to get the FULL design context for that specific node.
2. List out every design token you extracted (colors, fonts, sizes, spacing).
3. If anything is unclear or missing, ask me before proceeding.

### Step 2: Set Up Globals
1. Set global colors in Elementor Site Settings matching exact Figma hex values.
2. Set global fonts in Elementor Site Settings matching exact Figma font families and weights.

### Step 3: Build Desktop
1. Build the page section by section, top to bottom.
2. Use only Elementor Flexbox containers and native widgets.
3. Match every spec from Figma exactly.

### Step 4: Add Responsive Overrides
1. After desktop is complete, add tablet overrides following the tablet rules above.
2. Then add mobile overrides following the mobile rules above.

### Step 5: Add Custom CSS & JS
1. Apply all custom CSS to the appropriate sections/widgets in Elementor.
2. Apply any requested custom JS via Elementor's custom code injection.

### Step 6: Deliver Output
After building, provide me with THREE deliverables:

**Deliverable 1: Build Summary**
- What was built and what it looks like on each breakpoint
- Any elements that couldn't be matched exactly and why
- Any manual adjustments needed in Elementor editor

**Deliverable 2: Custom CSS Document**
```
/* ================================
   CUSTOM CSS — [Page Name]
   Generated: [Date]
   ================================ */

/* --- Section: Hero --- */
/* Applied to: container with class "hero-section" */
selector .hero-heading {
  /* styles here */
}

/* --- Section: Features --- */
/* Applied to: container with class "features-section" */
selector .feature-card {
  /* styles here */
}

/* ... etc for all sections ... */
```

**Deliverable 3: Custom JS Document (only if interactions were requested)**
```
/* ================================
   CUSTOM JS — [Page Name]
   Generated: [Date]
   Paste in: Elementor → Custom Code → Body
   ================================ */

document.addEventListener('DOMContentLoaded', function() {
  // Interaction code here with comments
});
```

---

## WordPress Site Info
- URL: http://my-site.local
- Admin: admin
- Elementor version: Free (no Pro widgets)
- Theme: Hello Elementor (recommended) or any lightweight theme

## Do NOT
- ❌ Add styles not in Figma
- ❌ Add hover effects unless I ask
- ❌ Use absolute/fixed/sticky positioning
- ❌ Use HTML widget or shortcode widget for layout/content
- ❌ Use jQuery
- ❌ Guess when specs are unclear — ask me
- ❌ Skip any detail from the Figma design
- ❌ Use fixed pixel widths for layout columns (use percentages or flex)

## DO
- ✅ Match Figma pixel-for-pixel on desktop
- ✅ Extract every spec down to letter-spacing and opacity
- ✅ Use only native Elementor widgets
- ✅ Use Flexbox containers for all layout
- ✅ Generate responsive tablet and mobile from desktop design
- ✅ Provide complete CSS and JS documents for production copy-paste
- ✅ Report what was built and what needs manual attention
- ✅ Ask me when something is unclear
