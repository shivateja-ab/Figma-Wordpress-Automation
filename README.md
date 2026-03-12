# Figma → Elementor AI Workflow

> Build Elementor pages from Figma designs using AI. No manual drag-and-drop.

```
Figma Design → [Figma MCP] → Claude Code → [Elementor MCP] → Elementor Page
```

## How It Works

Claude Code (AI coding agent) connects to two MCP servers:

- **Figma MCP** — reads your design (colors, layout, typography, spacing)
- **Elementor MCP** — builds the page (creates containers, widgets, styles, CSS/JS)

You provide a Figma URL, Claude does the rest. You review and iterate.

## Cost

| Component | Cost |
| --- | --- |
| LocalWP + WordPress + Elementor Free | Free |
| MCP Adapter + Elementor MCP plugins | Free (open-source) |
| Figma MCP / Framelink MCP | Free |
| **Option A:** Claude Pro subscription | $20/month per engineer |
| **Option B:** Ollama (local AI models) | **Completely free** |

## Choose Your AI Backend

### Option A: Claude Pro ($20/month) — Best quality

Uses Anthropic's models (Opus 4.6 / Sonnet 4.6). Best results for complex multi-section pages with responsive breakpoints. Recommended if budget allows.

### Option B: Ollama (Free) — Good for most tasks

Runs open-source AI models locally on your machine. No API costs. Quality is slightly lower for complex tasks but handles 80%+ of Elementor builds well.

**Ollama model recommendations by RAM:**

| RAM | Model | Command |
| --- | --- | --- |
| 8GB | `qwen3.5:4b` | `ollama pull qwen3.5:4b` |
| 16GB | `qwen3.5:9b` | `ollama pull qwen3.5:9b` |
| 24GB+ | `qwen3.5:27b` | `ollama pull qwen3.5:27b` |
| Any (cloud) | `qwen3-coder:480b-cloud` | `ollama pull qwen3-coder:480b-cloud` |

> **Note:** Local models won't match Anthropic's Opus on very complex tasks, but for straightforward Elementor builds from clear Figma designs, they work well. For complex responsive layouts with lots of custom interactions, Claude Pro gives noticeably better results.

---

## Setup Guide

### Step 1: Install LocalWP

1. Download from [localwp.com](https://localwp.com) (free, Mac/Windows/Linux)
2. Open LocalWP → **Create a New Site** → name it → choose "Preferred" environment
3. Set username `admin`, any password → click **Add Site**

### Step 2: Configure WordPress

1. Click **"WP Admin"** in LocalWP
2. **Settings → Permalinks** → select **"Post name"** → Save

   > ⚠️ **This is mandatory.** MCP endpoints do not work with "Plain" permalinks.

3. **Plugins → Add New** → search "Elementor" → Install & Activate
4. **Users → Profile** → scroll to "Application Passwords" → name it `claude-mcp` → **Add New** → **copy and save the password immediately**

### Step 3: Install MCP Plugins

**MCP Adapter:**

1. Download the latest `.zip` from [github.com/WordPress/mcp-adapter/releases](https://github.com/WordPress/mcp-adapter/releases)
2. WordPress Admin → **Plugins → Add New → Upload Plugin** → install & activate

**Elementor MCP:**

1. Download ZIP from [github.com/msrbuilds/elementor-mcp](https://github.com/msrbuilds/elementor-mcp) (green Code button → Download ZIP)
2. WordPress Admin → **Plugins → Add New → Upload Plugin** → install & activate
3. Go to **Settings → MCP Tools for Elementor** → verify tools are ON

### Step 4: Get Your Site Path

In LocalWP → right-click your site → "Go to Site Folder" → navigate to `app/public` → copy the full path.

Examples:

- **Mac:** `/Users/yourname/Local Sites/my-site/app/public`
- **Windows:** `C:\Users\yourname\Local Sites\my-site\app\public`

### Step 5: Install Claude Code

**Mac/Linux:**

```bash
curl -fsSL https://code.claude.com/install | sh
```

**Windows (PowerShell as Admin):**

```powershell
irm https://code.claude.com/install.ps1 | iex
```

> Windows also needs [Git for Windows](https://git-scm.com/install/win).

Verify:

```bash
claude --version
```

### Step 6: Install Ollama (Skip if using Claude Pro)

1. Download from [ollama.com](https://ollama.com) → install
2. Pull a model:

```bash
# Pick ONE based on your RAM:
ollama pull qwen3.5:4b              # 8GB RAM
ollama pull qwen3.5:9b              # 16GB RAM
ollama pull qwen3.5:27b             # 24GB+ RAM

# OR use cloud model (runs remotely, free tier):
ollama pull qwen3-coder:480b-cloud
```

### Step 7: Create Project Folder & Config Files

```bash
mkdir figma-to-elementor && cd figma-to-elementor
```

**Add `CLAUDE.md`** — this file gives Claude Code persistent rules for every session (pixel-perfect accuracy, no absolute positioning, pure Elementor widgets, responsive generation, CSS/JS deliverables). See [`CLAUDE.md`](./CLAUDE.md) in this repo.

Create a file named `.mcp.json`:

```json
{
  "mcpServers": {
    "elementor-mcp": {
      "type": "stdio",
      "command": "wp",
      "args": [
        "mcp-adapter", "serve",
        "--server=elementor-mcp-server",
        "--user=admin",
        "--path=YOUR_SITE_PATH_FROM_STEP_4"
      ]
    }
  }
}
```

Replace `--user` and `--path` with your actual values.

### Step 8: Launch Claude Code

**With Claude Pro:**

```bash
claude
```

Login via browser when prompted.

**With Ollama (free):**

```bash
ANTHROPIC_BASE_URL=http://localhost:11434 \
ANTHROPIC_AUTH_TOKEN=ollama \
ANTHROPIC_API_KEY="" \
claude --model qwen3.5:27b
```

**Pro tip:** Add an alias to `~/.zshrc` or `~/.bashrc` so you don't type this every time:

```bash
alias claude-free='ANTHROPIC_BASE_URL=http://localhost:11434 ANTHROPIC_AUTH_TOKEN=ollama ANTHROPIC_API_KEY="" claude --model qwen3.5:27b'
```

Then just run:

```bash
source ~/.zshrc   # reload once
claude-free       # use every time
```

### Step 9: Add Figma MCP

Inside Claude Code, add one of these:

**Official Figma MCP (needs paid Figma seat):**

```
claude mcp add figma --transport http https://mcp.figma.com/mcp
```

Then `/mcp` → select Figma → Authenticate in browser.

**Framelink MCP (works with free Figma):**

1. Generate a token at [figma.com/developers/api#access-tokens](https://www.figma.com/developers/api#access-tokens)
2. Run inside Claude Code:

```
claude mcp add framelink-figma npx -y figma-developer-mcp --figma-api-key=YOUR_TOKEN
```

### Step 10: Verify

Run inside Claude Code:

```
/mcp
```

Both `elementor-mcp` and `figma` (or `framelink-figma`) should show as connected.

Test:

```
List all available Elementor widgets on my site
```

If it returns a list, you're ready to build.

---

## Building Pages

### 1. Upload Images First

Export images from Figma (PNG @2x / SVG) → upload to WordPress **Media → Add New** → note the URLs.

### 2. The Build Prompt

Paste into Claude Code with your specific Figma node URL:

```
Build this Figma design in Elementor:
https://www.figma.com/design/FILEID/FileName?node-id=NODE_ID

Follow all rules in CLAUDE.md. Build section by section.
Provide the CSS and JS documents when done.
```

Claude Code will:
1. Read `CLAUDE.md` rules automatically
2. Pull exact specs from Figma via MCP (targeting your node-id)
3. Build the page in Elementor via MCP using only native widgets
4. Generate responsive tablet and mobile layouts
5. Give you a complete CSS file and JS file for production

### 3. Build Section by Section (Recommended)

Building one section at a time produces more accurate results:

```
Build only the Hero section from this Figma design: [URL]
```

Review in Elementor editor → fix issues → then:

```
Now build the Features section below the Hero.
```

### 4. Iterate

```
Make the heading 48px on tablet and 32px on mobile
```

```
Add scroll-triggered fade-in animations using IntersectionObserver
```

```
The cards need more gap on tablet, increase to 40px
```

---

## Deploy to Production

### Step 1: Export Template

**On your local site:**

1. Open page in Elementor → arrow next to "Update" → **Save as Template**
2. **Elementor → Templates → Saved Templates** → find it → **Export** (downloads `.json`)

### Step 2: Import Template

**On the production site:**

1. **Elementor → Templates → Saved Templates** → **Import** → upload the `.json`
2. Create/edit a page → Edit with Elementor → folder icon → My Templates → **Insert**
3. Re-upload images to production media library and update image URLs

### Step 3: Apply Custom CSS & JS

1. Copy the **Custom CSS document** from the build output
2. Paste into the production site: **Elementor → Page Settings → Custom CSS** (or use "Simple Custom CSS and JS" plugin)
3. Copy the **Custom JS document** from the build output
4. Paste via **Elementor → Custom Code → Add New** (body section) or the CSS/JS plugin

### Step 4: Post-Deploy Cleanup

1. **Elementor → Tools → Replace URL** → replace `.local` URL with production URL
2. **Elementor → Tools → Clear Files & Data** → regenerate CSS
3. Test desktop, tablet, and mobile on the live site

### What Transfers

| Element | Via Template (.json) | Via CSS/JS Documents |
| --- | --- | --- |
| Layout & structure | ✅ | — |
| Widget settings & content | ✅ | — |
| Elementor inline styles | ✅ | — |
| Custom CSS | ✅ (if added to sections locally) | ✅ Copy-paste to production |
| Custom JavaScript | ❌ | ✅ Copy-paste to production |
| Images | ❌ Re-upload manually | — |
| Global colors/fonts | ❌ Set up on production | — |

### Naming Convention

Use consistent names for exported templates:

```
[PageName]-v[Version]-[YYYY-MM-DD].json
```

Example: `Homepage-v2-2026-03-12.json`

---

## Troubleshooting

| Problem | Fix |
| --- | --- |
| MCP server not found | Check `.mcp.json` is in your current directory. Restart Claude Code. |
| REST API not responding | Change permalinks from "Plain" to "Post name". |
| Auth failed | Regenerate Application Password. Keep the spaces. |
| Plugins not detected | Verify MCP Adapter AND Elementor MCP are both activated. |
| Figma MCP won't auth | Run `/mcp` → re-authenticate via browser. |
| Ollama model too slow | Use a smaller model or switch to `qwen3-coder:480b-cloud`. |
| Responsive breaks | Build desktop first, then override tablet, then mobile. Never edit mobile first. |
| Custom CSS not applying | Elementor Free doesn't support widget-level CSS. Use page-level CSS or "Simple Custom CSS and JS" plugin. |

---

## Repo Structure

```
figma-to-elementor/
├── CLAUDE.md              # AI build rules (pixel-perfect, responsive, no absolute pos)
├── .mcp.json              # MCP server configuration
├── exports/               # Exported Elementor templates (.json)
│   └── Homepage-v1-2026-03-12.json
├── css/                   # Custom CSS documents per page
│   └── Homepage-custom.css
├── js/                    # Custom JS documents per page
│   └── Homepage-interactions.js
└── README.md              # This documentation
```

---

## Best Practices

- **Build one section at a time** — more accurate than full-page prompts
- **Use Flexbox containers** — not legacy sections/columns
- **Use percentage widths** — not fixed pixels for layout
- **Export decorative shapes as SVG** from Figma before building
- **Test all 3 breakpoints** (desktop, tablet, mobile) after each section
- **Save templates frequently** — easy to revert if something breaks
- **One person deploys to production** — avoids conflicts on the live site

---

## Quick Reference

```bash
# === ONE-TIME SETUP ===

# Install Claude Code
curl -fsSL https://code.claude.com/install | sh          # Mac/Linux
irm https://code.claude.com/install.ps1 | iex            # Windows

# Install Ollama (optional, for free usage)
# Download from https://ollama.com
ollama pull qwen3.5:27b

# === DAILY USE ===

cd figma-to-elementor

# Launch with Claude Pro
claude

# Launch with Ollama (free)
claude-free
# (requires alias from Step 8)

# Verify MCP connections inside Claude Code
/mcp

# Build a page — paste your Figma URL and go
```

---

## Links

| Resource | URL |
| --- | --- |
| LocalWP | https://localwp.com |
| Elementor | https://wordpress.org/plugins/elementor/ |
| MCP Adapter Plugin | https://github.com/WordPress/mcp-adapter/releases |
| Elementor MCP Plugin | https://github.com/msrbuilds/elementor-mcp |
| Ollama | https://ollama.com |
| Claude Code Install Docs | https://code.claude.com/docs/en/quickstart |
| Figma Personal Access Tokens | https://www.figma.com/developers/api#access-tokens |
| Figma MCP Docs | https://help.figma.com/hc/en-us/articles/32132100833559 |

---

*v1.0 — March 2026*
