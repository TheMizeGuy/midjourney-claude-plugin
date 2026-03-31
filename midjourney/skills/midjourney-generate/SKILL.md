---
name: midjourney-generate
description: Use when the user asks to generate, create, or imagine images using Midjourney, create wallpapers, make AI art, generate static assets (icons, backgrounds, textures, illustrations), or automate Midjourney via the web. Also use when user mentions Midjourney, MJ, or wants to create images with specific artistic styles. Requires the Playwright MCP plugin to be installed.
---

# Generating Images with Midjourney via Playwright

Drive Midjourney's web UI at midjourney.com using Playwright browser automation tools. This uses the user's existing Midjourney subscription directly -- no third-party APIs.

## Prerequisites

- Playwright MCP plugin must be installed and available
- User must have an active Midjourney subscription
- User must be logged in (or you help them log in)

## Login Flow

1. Navigate to `https://www.midjourney.com/imagine`
2. If not logged in, the page shows Sign Up / Log In buttons on the homepage
3. The homepage has a **canvas overlay** that blocks normal clicks. Fix it:
   ```js
   await page.evaluate(() => {
     document.querySelectorAll('canvas').forEach(c => c.style.pointerEvents = 'none');
   });
   ```
4. Then click Log In. A dialog/redirect will appear for Discord or Google auth.
5. If login requires user interaction (OAuth), tell the user to complete it in the browser window.
6. Once logged in, the URL changes to `/explore` or `/imagine` with the full navigation sidebar visible.

## Enforcing Stealth Mode

**Always verify stealth mode is ON before generating.** This prevents images from being public.

1. On the `/imagine` page, click the **settings icon** (sliders) next to the prompt textbox
2. In the settings panel under "More Options", check that **Stealth** shows **On** (highlighted)
3. If Off, click the "On" button to enable it
4. Close settings by pressing Escape or clicking elsewhere

Alternatively, open settings via: `browser_navigate` to `/imagine`, then find and click the settings button (the sliders/filter icon right of the prompt textbox).

## Generating Images

### Basic Generation Flow

1. Navigate to `https://www.midjourney.com/imagine`
2. Click the prompt textbox: `getByRole('textbox', { name: 'What will you imagine?' })`
3. Type the prompt using `fill()` -- include all parameters inline:
   ```
   a serene mountain lake at golden hour --ar 16:9 --stylize 500
   ```
4. Press Enter to submit
5. **Wait for generation** -- standard takes 30-90 seconds, draft 10-15 seconds
6. Scroll to top to see results -- new generations appear at the top of the page
7. The generation shows a progress percentage (e.g., "76% Complete") while running

### Using CLI Parameters in Prompts

Always append parameters to the end of the prompt text. This is more reliable than manipulating the settings panel UI. Key parameters:

- `--ar W:H` -- aspect ratio (e.g., `--ar 16:9`, `--ar 3:2`). MJ simplifies ratios automatically (21:9 becomes 7:3).
- `--stylize N` or `--s N` -- artistic interpretation strength (0-1000, default 100)
- `--chaos N` or `--c N` -- variation between grid images (0-100, default 0)
- `--weird N` or `--w N` -- unconventional qualities (0-3000, default 0)
- `--no items` -- negative prompt, exclude elements (e.g., `--no text words logos`)
- `--raw` or `--style raw` -- literal prompt interpretation, less MJ aesthetic
- `--seed N` -- reproducibility (0-4294967295)
- `--tile` -- seamless tileable pattern
- `--sref N` or `--sref URL` -- style reference
- `--cref URL` -- character reference (V6), `--oref URL` for V7+
- `--iw N` -- image weight (0-3, default 1)
- `--q N` -- quality (1 default, 2, 4)
- `--repeat N` -- run prompt multiple times
- `--stop N` -- stop generation at percentage (10-100)

### Speed Modes

Control via settings panel or commands:
- **Relax** -- unlimited, slower queue (saves fast hours)
- **Fast** -- priority queue, uses fast hours (default)
- **Turbo** -- 4x faster, uses 2x fast hours

For exploration/testing, switch to Relax to conserve hours.

### Draft Mode

Toggle via the **lightning bolt icon** in the top bar (turns orange when active). Draft generates ~4x faster at lower quality -- ideal for iterating on prompts before committing to a full generation.

## Waiting for Generation

Generation takes time. Use this pattern:

```js
// Wait with periodic checks
async (page) => {
  await page.waitForTimeout(60000); // 60s for standard, 15s for draft
  return "done";
}
```

Then take a screenshot to check progress. If you see a percentage indicator, wait longer. The generation is complete when the 4-image grid is fully rendered with no progress indicator.

## Selecting and Upscaling

1. After generation, click on the desired image in the grid (use `locator('a[href*="/jobs/"]')` to find image links)
2. The detail view shows **Creation Actions**:
   - **Vary**: Subtle (minor changes) / Strong (significant changes)
   - **Upscale**: Subtle (preserves original) / Creative (may enhance/alter details)
   - **More**: Rerun / Edit
   - **Use**: Image / Style / Prompt
3. For wallpapers/final output, click **Upscale > Subtle** for clean high-res results
4. Wait 30-60 seconds for upscale to complete
5. The upscaled image appears at the top of the page tagged "Upscale (S)" or "Upscale (C)"

Finding the upscale buttons:
```js
// The second "Subtle" button is for Upscale (first is for Vary)
page.getByRole('button', { name: 'Subtle' }).nth(1).click();
```

## Downloading Images

1. Open the upscaled image detail view
2. Find the download button: `page.locator('button[title="Download Image"]')`
3. Click it -- Playwright captures the download event
4. The file saves to `.playwright-mcp/` directory with a descriptive filename
5. Copy to desired location:
   ```bash
   cp ".playwright-mcp/filename.png" "/desired/path/image.png"
   ```

## Setting as Windows Wallpaper

```powershell
powershell -Command "Add-Type -TypeDefinition 'using System;using System.Runtime.InteropServices;public class Wallpaper{[DllImport(\"user32.dll\",CharSet=CharSet.Auto)]public static extern int SystemParametersInfo(int uAction,int uParam,string lpvParam,int fuWinIni);}'; [Wallpaper]::SystemParametersInfo(0x0014, 0, 'C:\path\to\image.png', 0x01 -bor 0x02)"
```
Return value 1 = success.

## Common Playwright Gotchas

### Canvas Overlays Blocking Clicks
The MJ homepage has a decorative canvas that intercepts pointer events. Fix:
```js
page.evaluate(() => {
  document.querySelectorAll('canvas').forEach(c => c.style.pointerEvents = 'none');
});
```

### Settings Panel Overlay
The settings panel creates a fixed overlay that blocks clicks on elements behind it. Press Escape to close it before interacting with other elements.

### HeadlessUI Dialog Overlays
Login and other modals use HeadlessUI with fixed overlays. These may need `page.evaluate()` to dismiss or interact through.

### Finding Elements by Title
Many buttons lack text labels. Use title attributes:
```js
page.locator('button[title="Download Image"]')
page.locator('button[title="Like Image (L)"]')
page.locator('button[title="Trash Image"]')
```

### Scrolling to New Content
New generations appear at the top. After submitting, scroll up:
```js
page.evaluate(() => {
  const scrollable = document.querySelector('[class*="overflow-y-auto"]');
  if (scrollable) scrollable.scrollTop = 0;
  window.scrollTo(0, 0);
});
```

## Midjourney Web Pages Reference

| Page | URL | Purpose |
|------|-----|---------|
| Create | /imagine | Main generation page |
| Explore | /explore | Browse community images |
| Editor | /editor/new | Inpainting, outpainting, retexture |
| Organize | /organize | Image library with filters |
| Personalize | /personalize | Aesthetic preference profiles |
| Moodboards | /moodboards | Curate style collections |
| Style Creator | /style-creator | Build custom --sref codes (Beta) |
| Tasks | /tasks | Rank images, earn fast hours |
| Account | /account | Subscription management |

## Editor (Inpainting/Outpainting)

1. From an image detail view, click **Edit** in Creation Actions
2. URL becomes `/edit/{job-id}?index=N`
3. Editor tools:
   - **Erase brush** -- paint over areas to regenerate (inpainting)
   - **Restore brush** -- undo erased areas
   - **Smart Select** -- AI-powered region selection
   - **Resize handles** -- drag edges to extend canvas (outpainting)
   - **Retexture tab** -- apply new textures/styles to the image
4. Modify the prompt in the top bar to guide regeneration
5. Click **Submit Edit** to process
6. Aspect Ratio presets: 1:1, 3:4, 2:3, 9:16, 1:2, 4:3, 3:2, 16:9, 2:1

## Video/Animation

From an image detail view:
- **Animate Image > Auto**: Low Motion (subtle, still) / High Motion (dynamic, may glitch)
- **Animate Image > Loop**: Creates looping video
- **Animate Manually**: More control over animation
- Settings: Video Resolution (SD/HD), Video Batch Size (1/2/4)
