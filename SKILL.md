---
name: frontend-slides
description: Create stunning, animation-rich HTML presentations from scratch or by converting PowerPoint files. Use when the user wants to build a presentation, convert a PPT/PPTX to web, or create slides for a talk/pitch. Helps non-designers discover their aesthetic through visual exploration rather than abstract choices.
---

# Frontend Slides

Create zero-dependency, animation-rich HTML presentations that run entirely in the browser.

## Core Principles

1. **Zero Dependencies** — Single HTML files with inline CSS/JS. No npm, no build tools.
2. **Show, Don't Tell** — Generate visual previews, not abstract choices. People discover what they want by seeing it.
3. **Distinctive Design** — No generic "AI slop." Every presentation must feel custom-crafted.
4. **Viewport Fitting (NON-NEGOTIABLE)** — Every slide MUST fit exactly within 100vh. No scrolling within slides, ever. Content overflows? Split into multiple slides.

## Design Aesthetics

You tend to converge toward generic, "on distribution" outputs. In frontend design, this creates what users call the "AI slop" aesthetic. Avoid this: make creative, distinctive frontends that surprise and delight.

Focus on:

- Typography: Choose fonts that are beautiful, unique, and interesting. Avoid generic fonts like Arial and Inter; opt instead for distinctive choices that elevate the frontend's aesthetics.
- Color & Theme: Commit to a cohesive aesthetic. Use CSS variables for consistency. Dominant colors with sharp accents outperform timid, evenly-distributed palettes. Draw from IDE themes and cultural aesthetics for inspiration.
- Motion: Use animations for effects and micro-interactions. Prioritize CSS-only solutions for HTML. Use Motion library for React when available. Focus on high-impact moments: one well-orchestrated page load with staggered reveals (animation-delay) creates more delight than scattered micro-interactions.
- Backgrounds: Create atmosphere and depth rather than defaulting to solid colors. Layer CSS gradients, use geometric patterns, or add contextual effects that match the overall aesthetic.

Avoid generic AI-generated aesthetics:

- Overused font families (Inter, Roboto, Arial, system fonts)
- Cliched color schemes (particularly purple gradients on white backgrounds)
- Predictable layouts and component patterns
- Cookie-cutter design that lacks context-specific character

Interpret creatively and make unexpected choices that feel genuinely designed for the context. Vary between light and dark themes, different fonts, different aesthetics. You still tend to converge on common choices (Space Grotesk, for example) across generations. Avoid this: it is critical that you think outside the box!

## Viewport Fitting Rules

These invariants apply to EVERY slide in EVERY presentation:

- Every `.slide` must have `height: 100vh; height: 100dvh; overflow: hidden;`
- ALL font sizes and spacing must use `clamp(min, preferred, max)` — never fixed px/rem
- Content containers need `max-height` constraints
- Images: `max-height: min(50vh, 400px)`
- Breakpoints required for heights: 700px, 600px, 500px
- Include `prefers-reduced-motion` support
- Never negate CSS functions directly (`-clamp()`, `-min()`, `-max()` are silently ignored) — use `calc(-1 * clamp(...))` instead

**When generating, read `viewport-base.css` and include its full contents in every presentation.**

### Content Density Limits Per Slide

| Slide Type    | Maximum Content                                           |
| ------------- | --------------------------------------------------------- |
| Title slide   | 1 heading + 1 subtitle + optional tagline                 |
| Content slide | 1 heading + 4-6 bullet points OR 1 heading + 2 paragraphs |
| Feature grid  | 1 heading + 6 cards maximum (2x3 or 3x2)                  |
| Code slide    | 1 heading + 8-10 lines of code                            |
| Quote slide   | 1 quote (max 3 lines) + attribution                       |
| Image slide   | 1 heading + 1 image (max 60vh height)                     |

**Content exceeds limits? Split into multiple slides. Never cram, never scroll.**

---

## Phase 0: Detect Mode

Determine what the user wants:

- **Mode A: New Presentation** — Create from scratch. Go to Phase 1.
- **Mode B: PPT Conversion** — Convert a .pptx file. Go to Phase 4.
- **Mode C: Enhancement** — Improve an existing HTML presentation. Read it, understand it, enhance. **Follow Mode C modification rules below.**
- **Mode D: Long-scroll Infographic** — Create a vertical-scrolling infographic optimized for mobile/social media. Go to Phase 7.

### Mode C: Modification Rules

When enhancing existing presentations, viewport fitting is the biggest risk:

1. **Before adding content:** Count existing elements, check against density limits
2. **Adding images:** Must have `max-height: min(50vh, 400px)`. If slide already has max content, split into two slides
3. **Adding text:** Max 4-6 bullets per slide. Exceeds limits? Split into continuation slides
4. **After ANY modification, verify:** `.slide` has `overflow: hidden`, new elements use `clamp()`, images have viewport-relative max-height, content fits at 1280x720
5. **Proactively reorganize:** If modifications will cause overflow, automatically split content and inform the user. Don't wait to be asked

**When adding images to existing slides:** Move image to new slide or reduce other content first. Never add images without checking if existing content already fills the viewport.

---

## Phase 1: Content Discovery (New Presentations)

**Ask ALL questions in a single AskUserQuestion call** so the user fills everything out at once:

**Question 1 — Purpose** (header: "Purpose"):
What is this presentation for? Options: Pitch deck / Teaching-Tutorial / Conference talk / Internal presentation

**Question 2 — Length** (header: "Length"):
Approximately how many slides? Options: Short 5-10 / Medium 10-20 / Long 20+

**Question 3 — Content** (header: "Content"):
Do you have content ready? Options: All content ready / Rough notes / Topic only

**Question 4 — Inline Editing** (header: "Editing"):
Do you need to edit text directly in the browser after generation? Options:

- "Yes (Recommended)" — Can edit text in-browser, auto-save to localStorage, export file
- "No" — Presentation only, keeps file smaller

**Remember the user's editing choice — it determines whether edit-related code is included in Phase 3.**

If user has content, ask them to share it.

### Step 1.2: Image Evaluation (if images provided)

If user selected "No images" → skip to Phase 2.

If user provides an image folder:

1. **Scan** — List all image files (.png, .jpg, .svg, .webp, etc.)
2. **View each image** — Use the Read tool (Claude is multimodal)
3. **Evaluate** — For each: what it shows, USABLE or NOT USABLE (with reason), what concept it represents, dominant colors
4. **Co-design the outline** — Curated images inform slide structure alongside text. This is NOT "plan slides then add images" — design around both from the start (e.g., 3 screenshots → 3 feature slides, 1 logo → title/closing slide)
5. **Confirm via AskUserQuestion** (header: "Outline"): "Does this slide outline and image selection look right?" Options: Looks good / Adjust images / Adjust outline

**Logo in previews:** If a usable logo was identified, embed it (base64) into each style preview in Phase 2 — the user sees their brand styled three different ways.

---

## Phase 2: Style Discovery

**This is the "show, don't tell" phase.** Most people can't articulate design preferences in words.

### Step 2.0: Style Path

Ask how they want to choose (header: "Style"):

- "Show me options" (recommended) — Generate 3 previews based on mood
- "I know what I want" — Pick from preset list directly

**If direct selection:** Show preset picker and skip to Phase 3. Available presets are defined in [STYLE_PRESETS.md](STYLE_PRESETS.md).

### Step 2.1: Mood Selection (Guided Discovery)

Ask (header: "Vibe", multiSelect: true, max 2):
What feeling should the audience have? Options:

- Impressed/Confident — Professional, trustworthy
- Excited/Energized — Innovative, bold
- Calm/Focused — Clear, thoughtful
- Inspired/Moved — Emotional, memorable

### Step 2.2: Generate 3 Style Previews

Based on mood, generate 3 distinct single-slide HTML previews showing typography, colors, animation, and overall aesthetic. Read [STYLE_PRESETS.md](STYLE_PRESETS.md) for available presets and their specifications.

| Mood                | Suggested Presets                                  |
| ------------------- | -------------------------------------------------- |
| Impressed/Confident | Bold Signal, Electric Studio, Dark Botanical       |
| Excited/Energized   | Creative Voltage, Neon Cyber, Split Pastel         |
| Calm/Focused        | Notebook Tabs, Paper & Ink, Swiss Modern           |
| Inspired/Moved      | Dark Botanical, Vintage Editorial, Pastel Geometry |

Save previews to `.claude-design/slide-previews/` (style-a.html, style-b.html, style-c.html). Each should be self-contained, ~50-100 lines, showing one animated title slide.

Open each preview automatically for the user.

### Step 2.3: User Picks

Ask (header: "Style"):
Which style preview do you prefer? Options: Style A: [Name] / Style B: [Name] / Style C: [Name] / Mix elements

If "Mix elements", ask for specifics.

---

## Phase 3: Generate Presentation

Generate the full presentation using content from Phase 1 (text, or text + curated images) and style from Phase 2.

If images were provided, the slide outline already incorporates them from Step 1.2. If not, CSS-generated visuals (gradients, shapes, patterns) provide visual interest — this is a fully supported first-class path.

**Before generating, read these supporting files:**

- [html-template.md](html-template.md) — HTML architecture and JS features
- [viewport-base.css](viewport-base.css) — Mandatory responsive CSS (include in full)
- [animation-patterns.md](animation-patterns.md) — Animation reference for the chosen feeling

**Key requirements:**

- Single self-contained HTML file, all CSS/JS inline
- Include the FULL contents of viewport-base.css in the `<style>` block
- Use fonts from Fontshare or Google Fonts — never system fonts
- Add detailed comments explaining each section
- Every section needs a clear `/* === SECTION NAME === */` comment block

**File output location:**
- HTML file: `~/downloads/[filename].html`
- All exported images/PDFs: `~/downloads/`

---

## Phase 4: PPT Conversion

When converting PowerPoint files:

1. **Extract content** — Run `python scripts/extract-pptx.py <input.pptx> <output_dir>` (install python-pptx if needed: `pip install python-pptx`)
2. **Confirm with user** — Present extracted slide titles, content summaries, and image counts
3. **Style selection** — Proceed to Phase 2 for style discovery
4. **Generate HTML** — Convert to chosen style, preserving all text, images (from assets/), slide order, and speaker notes (as HTML comments)

---

## Phase 5: Delivery

1. **Clean up** — Delete `.claude-design/slide-previews/` if it exists
2. **Move to downloads** — Move the final HTML file to `~/downloads/`
3. **Open** — Use `open ~/downloads/[filename].html` to launch in browser
4. **Summarize** — Tell the user:
   - File location (`~/downloads/[filename].html`), style name, slide count
   - Navigation: Arrow keys, Space, scroll/swipe, click nav dots
   - How to customize: `:root` CSS variables for colors, font link for typography, `.reveal` class for animations
   - If inline editing was enabled: Hover top-left corner or press E to enter edit mode, click any text to edit, Ctrl+S to save

---

## Phase 6: Share & Export (Optional)

After delivery, **ask the user:** _"Would you like to share this presentation? I can deploy it to a live URL (works on any device including phones) or export it as a PDF or image."_

Options:

- **Deploy to URL** — Shareable link that works on any device
- **Export to PDF** — Universal file for email, Slack, print
- **Export to Image** — Long-scroll image for social media
- **Both**
- **No thanks**

If the user declines, stop here. If they choose one or both, proceed below.

### 6A: Deploy to a Live URL (Vercel)

This deploys the presentation to Vercel — a free hosting platform. The link works on any device (phones, tablets, laptops) and stays live until the user takes it down.

**If the user has never deployed before, guide them step by step:**

1. **Check if Vercel CLI is installed** — Run `npx vercel --version`. If not found, install Node.js first (`brew install node` on macOS, or download from https://nodejs.org).

2. **Check if user is logged in** — Run `npx vercel whoami`.
   - If NOT logged in, explain: _"Vercel is a free hosting service. You need an account to deploy. Let me walk you through it:"_
     - Step 1: Ask user to go to https://vercel.com/signup in their browser
     - Step 2: They can sign up with GitHub, Google, email — whatever is easiest
     - Step 3: Once signed up, run `vercel login` and follow the prompts (it opens a browser window to authorize)
     - Step 4: Confirm login with `vercel whoami`
   - Wait for the user to confirm they're logged in before proceeding.

3. **Deploy** — Run the deploy script:

   ```bash
   bash scripts/deploy.sh <path-to-presentation>
   ```

   The script accepts either a folder (with index.html) or a single HTML file.

4. **Share the URL** — Tell the user:
   - The live URL (from the script output)
   - That it works on any device — they can text it, Slack it, email it
   - To take it down later: visit https://vercel.com/dashboard and delete the project
   - The Vercel free tier is generous — they won't be charged

**⚠ Deployment gotchas:**

- **Local images/videos must travel with the HTML.** The deploy script auto-detects files referenced via `src="..."` in the HTML and bundles them. But if the presentation references files via CSS `background-image` or unusual paths, those may be missed. **Before deploying, verify:** open the deployed URL and check that all images load. If any are broken, the safest fix is to put the HTML and all its assets into a single folder and deploy the folder instead of a standalone HTML file.
- **Prefer folder deployments when the presentation has many assets.** If the presentation lives in a folder with images alongside it (e.g., `my-deck/index.html` + `my-deck/logo.png`), deploy the folder directly: `bash scripts/deploy.sh ./my-deck/`. This is more reliable than deploying a single HTML file because the entire folder contents are uploaded as-is.
- **Filenames with spaces work but can cause issues.** The script handles spaces in filenames, but Vercel URLs encode spaces as `%20`. If possible, avoid spaces in image filenames. If the user's images have spaces, the script handles it — but if images still break, renaming files to use hyphens instead of spaces is the fix.
- **Redeploying updates the same URL.** Running the deploy script again on the same presentation overwrites the previous deployment. The URL stays the same — no need to share a new link.

### 6B: Export to PDF

This captures each slide as a screenshot and combines them into a PDF. Perfect for email attachments, embedding in documents, or printing.

**Note:** Animations and interactivity are not preserved — the PDF is a static snapshot. This is normal and expected; mention it to the user so they're not surprised.

1. **Run the export script:**

   ```bash
   bash scripts/export-pdf.sh <path-to-html> [output.pdf]
   ```

   If no output path is given, the PDF is saved to `~/downloads/`.

2. **What happens behind the scenes** (explain briefly to the user):
   - A headless browser opens the presentation at 1920×1080 (standard widescreen)
   - It screenshots each slide one by one
   - All screenshots are combined into a single PDF
   - The script needs Playwright (a browser automation tool) — it will install automatically if missing

3. **If Playwright installation fails:**
   - The most common issue is Chromium not downloading. Run: `npx playwright install chromium`
   - If that fails too, it may be a network/firewall issue. Ask the user to try on a different network.

4. **Deliver the PDF** — The script auto-opens it. Tell the user:
   - The file location (`~/downloads/[filename].pdf`) and size
   - That it works everywhere — email, Slack, Notion, Google Docs, print
   - Animations are replaced by their final visual state (still looks great, just static)

**⚠ PDF export gotchas:**

- **First run is slow.** The script installs Playwright and downloads a Chromium browser (~150MB) into a temp directory. This happens once per run. Warn the user it may take 30-60 seconds the first time — subsequent exports within the same session are faster.
- **Slides must use `class="slide"`.** The export script finds slides by querying `.slide` elements. If the presentation uses a different class name, the script will report "0 slides found" and fail. All presentations generated by this skill use `.slide`, so this only matters for externally-created HTML.
- **Local images must be loadable via HTTP.** The script starts a local server and loads the HTML through it (so Google Fonts and relative image paths work). If images use absolute filesystem paths (e.g., `src="/Users/name/photo.png"`) instead of relative paths (e.g., `src="photo.png"`), they won't load. Generated presentations always use relative paths, but converted or user-provided decks might not — check and fix if needed.
- **Local images appear in the PDF** as long as they are in the same directory as (or relative to) the HTML file. The export script serves the HTML's parent directory over HTTP, so relative paths like `src="photo.png"` resolve correctly — including filenames with spaces. If images still don't appear, check: (1) the image files actually exist at the referenced path, (2) the paths are relative, not absolute filesystem paths like `/Users/name/photo.png`.
- **Large presentations produce large PDFs.** Each slide is captured as a full 1920×1080 PNG screenshot. An 18-slide deck can produce a ~20MB PDF. If the PDF exceeds 10MB, ask the user: _"The PDF is [size]. Would you like me to compress it? It'll look slightly less sharp but the file will be much smaller."_ If yes, re-run the export with the `--compact` flag:
  ```bash
  bash scripts/export-pdf.sh <path-to-html> [output.pdf] --compact
  ```
  This renders at 1280×720 instead of 1920×1080, typically cutting file size by 50-70% with minimal visual difference.

### 6C: Export to Image (Long-scroll)

This exports the presentation as a single long-scroll image, perfect for sharing on social media like WeChat Moments.

1. **Run the export script:**

   ```bash
   bash scripts/export-long-image.sh <path-to-html> [output.png]
   ```

   If no output path is given, the image is saved to `~/downloads/`.

2. **Output specifications:**
   - Width: 800px (optimized for mobile)
   - Height: Full page height (varies by content)
   - Format: PNG
   - File location: `~/downloads/[filename]-long.png`

3. **Use cases:**
   - WeChat Moments / 朋友圈
   - Xiaohongshu / 小红书
   - Any platform that prefers single long images over multi-page PDFs

---

## Phase 7: Long-scroll Infographic (Mobile/Social Media)

This mode creates vertical-scrolling infographics optimized for mobile screens and social media sharing (WeChat Moments, Xiaohongshu, etc.). Unlike slide presentations, long-scroll infographics use a continuous vertical layout where "slides" stack on top of each other.

### 7.1: When to Use This Mode

Use long-scroll infographic mode when:
- The user explicitly asks for "信息图" (infographic)
- The content is meant to be shared on social media
- The target audience will view it primarily on mobile phones
- The user needs a single image file (PNG) rather than an interactive presentation

### 7.2: Style Presets for Long-scroll Infographics

**Available styles:**

1. **Song Elegance（宋韵美学）** — Default for Chinese cultural/health/medical content
   - Warm cream background (#f7f5f0)
   - Serif Chinese fonts (Noto Serif SC)
   - Ink-wash aesthetics, seal stamps, generous whitespace
   - Best for: 中医、养生、传统文化、读书分享

2. **Apple Minimal（黑白苹果风）** — Modern, clean, high contrast
   - Pure white background
   - Black text, gray secondary text
   - Rounded cards (20px), system fonts
   - Best for: 科技、科普、现代知识传播

3. **Bold Editorial（ bold 编辑风）** — Magazine-style impact
   - Strong typography hierarchy
   - Vibrant accent colors on neutral backgrounds
   - Best for: 商业、品牌、演讲稿

4. **半宋 (Ban Song / Balanced Song)** — **The balanced style: Song elegance + information clarity**
   - **Background**: Warm off-white `#faf9f6`, subtle radial gradients for atmosphere without distraction
   - **Typography**: Display titles in Noto Serif SC (宋体), body text in Noto Sans SC (黑体)
   - **Layout**: Card-based with clear borders (`1px solid #e7e5e4`) and light shadows
   - **Structure**: Numbered section headers (red circular badges) guide the eye through information hierarchy
   - **Colors**: Deepened Song palette — cinnabar `#9f2b1f`, jade `#4d6b3c`, antique gold `#8b6914`
   - **Width**: 520px container with minimal side padding (`0.25rem` left/right) for maximum content density
   - **Best for**: 中医科普、健康养生、需要兼顾美感与信息传递效率的内容

### 7.3: Mobile Typography Standards

**Default mobile sizing (standard):**

| Element | Font Size | Use Case |
|---------|-----------|----------|
| Main title | 1.75rem (~28px) | Hero section |
| Section title | 1.25rem (~20px) | Content headings |
| Body text | 0.85rem (~14px) | Paragraphs, descriptions |
| Tags/labels | 0.7rem (~11px) | Category tags |
| Captions | 0.75rem (~12px) | Small annotations |

**Large mobile sizing (for middle-aged/older users):**

| Element | Font Size | Use Case |
|---------|-----------|----------|
| Main title | 2.8rem (~45px) | Hero section |
| Section title | 1.9rem (~30px) | Content headings |
| Body text | 1.4rem (~22px) | Paragraphs, descriptions |
| Tags/labels | 1.4rem (~22px) | **Must match body text** |
| Captions | 1.2rem (~19px) | Small annotations |

**Extra-large mobile sizing (for users with presbyopia/vision impairment):**

| Element | Font Size | Use Case |
|---------|-----------|----------|
| Main title | 3.4rem (~54px) | Hero section |
| Section title | 2.4rem (~38px) | Content headings |
| Body text | 1.55rem (~25px) | Paragraphs, descriptions |
| Tags/labels | 1.55rem (~25px) | **Must match body text** |
| Captions | 1.55rem (~25px) | **Must match body text** |
| Container width | 480px | Narrower for easier reading |
| Line height | 2.0-2.4 | Generous spacing |
| Border width | 3px | Clear visual boundaries |

**Critical rule for accessibility:** In large and extra-large sizes, **all auxiliary text (tags, labels, captions, small print) must use the same font size as body text.** Do not make tags smaller than body text — this is the #1 complaint from older users.

### 7.4: 半宋 (Balanced Song) Typography Specification

When generating long-scroll infographics in the **半宋** style, use these exact specifications:

| Element | Font Size | Font Family | Notes |
|---------|-----------|-------------|-------|
| Hero title | 2.6rem (~42px) | Noto Serif SC | Main heading, centered |
| Hero subtitle | 1.35rem (~22px) | Noto Sans SC | Secondary tagline |
| Section number badge | 1.1rem (~18px) | Noto Serif SC | Circular red `#9f2b1f` badge |
| Section title | 1.75rem (~28px) | Noto Serif SC | Left border accent |
| Section subtitle | 1.85rem (~30px) | Noto Sans SC | Description under section title |
| Card title | 2.0rem (~32px) | Noto Sans SC | Bold, inside cards |
| Body text | **1.9rem (~30px)** | Noto Sans SC | **Primary readable text** |
| Feature list item | 1.85rem (~30px) | Noto Sans SC | Bullet points |
| Flow step content | 1.9rem (~30px) | Noto Sans SC | Process steps |
| Quote text | 1.95rem (~31px) | Noto Serif SC | Italic, highlighted quotes |
| Caption | 1.75rem (~28px) | Noto Sans SC | Visual labels, annotations |
| Bottom note | 1.75rem (~28px) | Noto Sans SC | Footer text |
| Summary title | 2.3rem (~37px) | Noto Serif SC | Dark background section |
| Tag/label | 1.1rem (~18px) | Noto Sans SC | Category tags inside cards |

**Layout rules for 半宋:**
- `.slide` padding: `2rem 0.25rem` (minimal side margins)
- `.slide-hero` padding: `3rem 0.25rem`
- Container `max-width`: 520px
- Card padding: `1.3rem`
- Card border-radius: `14px`
- Card border: `1px solid #e7e5e4`
- Section gap: `1.1rem`
- Line height for body: `1.85`

### 7.5: Contrast Requirements

For all mobile infographics, especially large-size versions:
- **Text color**: Minimum `#2c2c2c` on light backgrounds, preferably `#1a1a1a`
- **Secondary text**: Minimum `#4a4a4a`, never lighter than `#6b6b6b`
- **Accent colors**: Must be deep enough to read easily (e.g., `#8b3a2b` instead of `#a65d4d`)
- **Card backgrounds**: Use `rgba(255,255,255,0.7)` or solid light colors
- **Borders**: Minimum 2px for large size, 3px for extra-large

### 7.6: Export Naming Convention

When exporting long-scroll infographics, use this naming convention:

```
~/downloads/[filename]-mobile.png           # Standard size
~/downloads/[filename]-mobile-large.png     # Large text (middle-aged friendly)
~/downloads/[filename]-mobile-xl.png        # Extra-large text (vision impaired)
```

For style variants:
```
~/downloads/[filename]-apple-mobile.png     # Apple minimal style
~/downloads/[filename]-balanced-mobile.png  # 半宋 (Balanced Song) style
```

### 7.7: Long-scroll Export Process

1. Generate the HTML with vertical-stacking `.slide` elements (not 100vh)
2. Ensure `.reveal` elements default to `opacity: 1` (not hidden)
3. Save HTML to `~/downloads/[filename].html`
4. Export using:
   ```bash
   bash scripts/export-long-image.sh <path-to-html> [output.png]
   ```
5. Move final PNG to `~/downloads/`
6. Report both file locations to the user

---

## Supporting Files

| File                                               | Purpose                                                              | When to Read              |
| -------------------------------------------------- | -------------------------------------------------------------------- | ------------------------- |
| [STYLE_PRESETS.md](STYLE_PRESETS.md)               | 12 curated visual presets with colors, fonts, and signature elements | Phase 2 (style selection) |
| [viewport-base.css](viewport-base.css)             | Mandatory responsive CSS — copy into every presentation              | Phase 3 (generation)      |
| [html-template.md](html-template.md)               | HTML structure, JS features, code quality standards                  | Phase 3 (generation)      |
| [animation-patterns.md](animation-patterns.md)     | CSS/JS animation snippets and effect-to-feeling guide                | Phase 3 (generation)      |
| [scripts/extract-pptx.py](scripts/extract-pptx.py) | Python script for PPT content extraction                             | Phase 4 (conversion)      |
| [scripts/deploy.sh](scripts/deploy.sh)             | Deploy slides to Vercel for instant sharing                          | Phase 6 (sharing)         |
| [scripts/export-pdf.sh](scripts/export-pdf.sh)     | Export slides to PDF                                                 | Phase 6 (sharing)         |
| [scripts/export-long-image.sh](scripts/export-long-image.sh) | Export long-scroll image for social media                  | Phase 6 (sharing)         |
