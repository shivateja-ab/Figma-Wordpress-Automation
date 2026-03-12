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

### ABSOLUTELY NO HTML WIDGETS
- ❌ NEVER use the HTML widget. NEVER. Not for text, not for images, not for layout, not for anything.
- ❌ NEVER use the Shortcode widget.
- ❌ NEVER inject inline CSS via `<style>` tags inside any widget.
- ❌ NEVER inject inline JS via `<script>` tags inside any widget.
- ❌ NEVER put raw HTML, CSS, or JS inside any Elementor page content.
- If you find yourself wanting to use an HTML widget, STOP and use the correct native widget instead.

### Use ONLY These Native Elementor Widgets
- **Text content** → Heading widget (H1–H6) or Text Editor widget
- **Images** → Image widget
- **Buttons** → Button widget
- **Icons** → Icon widget or Icon Box widget
- **Spacing** → Spacer widget or container padding/gap
- **Dividers** → Divider widget
- **Videos** → Video widget
- **Ratings** → Star Rating widget
- **Lists** → Icon List widget
- **Carousels** → Image Carousel widget

Allowed widget list (use ONLY these):
`heading`, `text-editor`, `image`, `button`, `icon`, `icon-box`, `image-box`, `star-rating`, `divider`, `spacer`, `video`, `google_maps`, `progress`, `testimonial`, `tabs`, `accordion`, `toggle`, `social-icons`, `alert`, `counter`, `image-carousel`, `icon-list`

### Layout = Containers Only
- ALL layout is done with **nested Flexbox containers**.
- A card = a container with padding, background, border-radius, and child widgets inside.
- A row of cards = a parent container (flex-direction: row) with child card containers inside.
- A section = a full-width container with an inner max-width container.
- NEVER flatten layout into a single container with HTML. Always nest containers properly.

### ALL Styling Goes in the External CSS Plugin
- Do NOT add any CSS inside Elementor page settings.
- Do NOT add any CSS inside widget Advanced → Custom CSS.
- Do NOT use `<style>` tags anywhere in the page.
- ALL custom CSS goes into the **"Simple Custom CSS and JS"** plugin via WordPress Admin → Custom CSS & JS → Add Custom CSS.
- Assign CSS class names to containers and widgets using Elementor's **Advanced → CSS Classes** field, then target those classes in the external CSS page.

### ALL JavaScript Goes in the External JS Plugin
- Do NOT add any JS inside the page.
- Do NOT use `<script>` tags anywhere.
- ALL custom JS goes into the **"Simple Custom CSS and JS"** plugin via WordPress Admin → Custom CSS & JS → Add Custom JS.
- Load JS in the Footer as an External File.

### Custom CSS
- Add custom CSS using the **"Simple Custom CSS and JS"** plugin.
  - In WordPress Admin: **Custom CSS & JS → Add Custom CSS** → paste CSS there.
  - This creates a dedicated CSS page that loads site-wide and survives theme/page changes.
- For section-specific CSS, assign CSS classes to containers/widgets (Advanced → CSS Classes in Elementor), then target those classes in the custom CSS page.
- Do NOT rely on Elementor's widget-level Custom CSS (requires Pro).
- Write standard CSS selectors targeting your custom class names. Do NOT use Elementor's `selector` keyword in the plugin — that only works inside Elementor's own CSS fields.
- After building each page, provide me with a COMPLETE compiled document containing:
  - ALL custom CSS used, organized by section with clear comments
  - The CSS class names applied to each container/widget
  - Instructions on which class belongs to which element
- This CSS document is for copy-pasting into the **Custom CSS page** on both local and production sites.

### Custom JavaScript
- Add custom JS using the **"Simple Custom CSS and JS"** plugin.
  - In WordPress Admin: **Custom CSS & JS → Add Custom JS** → paste JS there.
  - Set loading location to **Footer** for best performance.
  - Set loading type to **External file** for caching benefits.
- Use vanilla JavaScript only. NEVER use jQuery.
- Only add JS if I explicitly request interactions (scroll animations, toggles, etc.).
- Wrap all JS in `document.addEventListener('DOMContentLoaded', function() { ... })`.
- After building, provide me with a COMPLETE compiled document containing:
  - ALL custom JS used, with comments explaining what each block does
  - Instructions for the Custom JS page configuration (header/footer, internal/external)
- This JS document is for copy-pasting into the **Custom JS page** on both local and production sites.

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
1. Install "Simple Custom CSS and JS" plugin on the local WordPress site if not already active.
2. Add all custom CSS via **Custom CSS & JS → Add Custom CSS** in WordPress Admin.
3. Add any requested custom JS via **Custom CSS & JS → Add Custom JS** in WordPress Admin.
4. Assign CSS classes to containers/widgets in Elementor (Advanced → CSS Classes) and target them in the CSS page.

### Step 6: Deliver Output
After building, provide me with THREE deliverables:

**Deliverable 1: Build Summary**
- What was built and what it looks like on each breakpoint
- Any elements that couldn't be matched exactly and why
- Any manual adjustments needed in Elementor editor

**Deliverable 2: Custom CSS Document**
For pasting into **Custom CSS & JS → Add Custom CSS** on both local and production.
```css
/* ================================
   CUSTOM CSS — [Page Name]
   Generated: [Date]
   Paste in: WP Admin → Custom CSS & JS → Add Custom CSS
   Load in: Header
   ================================ */

/* --- Section: Hero --- */
/* Class "hero-section" applied to the outer container */
.hero-section {
  /* styles here */
}

.hero-section .hero-heading {
  /* styles here */
}

/* --- Section: Features --- */
/* Class "features-section" applied to the outer container */
.features-section {
  /* styles here */
}

.features-section .feature-card {
  /* styles here */
}

/* --- Responsive: Tablet (1024px) --- */
@media (max-width: 1024px) {
  .hero-section .hero-heading {
    /* tablet overrides */
  }
}

/* --- Responsive: Mobile (767px) --- */
@media (max-width: 767px) {
  .hero-section .hero-heading {
    /* mobile overrides */
  }
}
```

**Deliverable 3: Custom JS Document (only if interactions were requested)**
For pasting into **Custom CSS & JS → Add Custom JS** on both local and production.
```js
/* ================================
   CUSTOM JS — [Page Name]
   Generated: [Date]
   Paste in: WP Admin → Custom CSS & JS → Add Custom JS
   Load in: Footer
   Type: External File
   ================================ */

document.addEventListener('DOMContentLoaded', function() {
  // Interaction code here with comments
});
```

---

## WordPress Site Info
- URL: http://my-site.local
- Admin: admin-name
- Elementor version: Free (no Pro widgets)
- Theme: Hello Elementor (recommended) or any lightweight theme

## Do NOT
- ❌ Use HTML widget — EVER, for ANY reason
- ❌ Use Shortcode widget
- ❌ Put `<style>` or `<script>` tags anywhere in the page
- ❌ Add CSS inside Elementor's page settings or widget Advanced tab
- ❌ Add JS inside the page content
- ❌ Add styles not in Figma
- ❌ Add hover effects unless I ask
- ❌ Use absolute/fixed/sticky positioning
- ❌ Use jQuery
- ❌ Guess when specs are unclear — ask me
- ❌ Skip any detail from the Figma design
- ❌ Use fixed pixel widths for layout columns (use percentages or flex)

## DO
- ✅ Match Figma pixel-for-pixel on desktop
- ✅ Extract every spec down to letter-spacing and opacity
- ✅ Use ONLY native Elementor widgets (heading, text-editor, image, button, icon, etc.)
- ✅ Use nested Flexbox containers for all layout
- ✅ Assign CSS classes via Advanced → CSS Classes on containers/widgets
- ✅ Put ALL custom CSS in the external "Simple Custom CSS and JS" plugin
- ✅ Put ALL custom JS in the external "Simple Custom CSS and JS" plugin
- ✅ Generate responsive tablet and mobile from desktop design
- ✅ Provide complete CSS and JS documents for production copy-paste
- ✅ Report what was built and what needs manual attention
- ✅ Ask me when something is unclear
