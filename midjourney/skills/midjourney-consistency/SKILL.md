---
name: midjourney-consistency
description: Use when generating multiple images that need to share a consistent visual style, theme, or design language -- such as a set of icons, a series of illustrations, branded assets, UI elements, or any batch of images for the same project. Also use when the user has existing project assets, screenshots, or design files and wants new images that match the established look and feel. Triggers on "match the style", "keep it consistent", "same look as", "make more like this", "design system", "themed set", "series of images", "brand-consistent", "match our existing".
---

# Maintaining Design Consistency Across Multiple Midjourney Generations

When generating multiple images for the same project, theme, or brand, use these techniques to keep everything visually cohesive.

## Strategy Overview

There are four main tools for consistency, in order of strength:

| Tool | What it controls | Strength |
|------|-----------------|----------|
| **Style Reference (--sref)** | Color palette, texture, mood, rendering style | Strongest visual consistency |
| **Omni Reference (--oref)** | Character/subject appearance across scenes | Strongest subject consistency |
| **Seed (--seed)** | Reproducible starting point for generation | Moderate -- same seed + similar prompt = similar feel |
| **Prompt structure** | Shared descriptive language | Foundation -- always use alongside other tools |

## Method 1: Style Reference Anchoring (Recommended)

The most reliable way to keep a consistent look across a set.

### Step 1: Generate your anchor image
Create the first image in your set. Get it exactly right -- this becomes the style source for everything else.

### Step 2: Extract the style
From the anchor image's detail view, click **Use > Style**. This copies the image as a `--sref` parameter you can paste into future prompts.

Alternatively, use the anchor image URL directly:
```
new subject or scene --sref https://cdn.midjourney.com/your-anchor-image-url.png --sw 200
```

### Step 3: Generate the rest of the set
Use the same `--sref` on every subsequent prompt. Only change the subject/content description:

```
a login page hero illustration --sref URL --sw 200 --ar 16:9 --s 500
a pricing page hero illustration --sref URL --sw 200 --ar 16:9 --s 500
an about page hero illustration --sref URL --sw 200 --ar 16:9 --s 500
```

### Tuning style weight (--sw)
- `--sw 100` (default): Moderate influence -- picks up color palette and general mood
- `--sw 200-400`: Strong influence -- closely matches rendering style, textures, lighting
- `--sw 600-1000`: Very strong -- may override prompt details in favor of style

For a themed set, **--sw 200-400** is usually the sweet spot.

### Style Codes
If you find a style you love, save its `--sref` code (numeric codes from the Style Creator at `/style-creator`). These are persistent and reusable across sessions:
```
icon design --sref 6281858160 --sw 300
```

## Method 2: Using Existing Project Assets as Inspiration

When a project already has established visual assets (logos, screenshots, illustrations, UI mockups), use them to ground new generations in the existing design language.

### Uploading project assets via Playwright

1. Navigate to `/imagine`
2. Click the **Add Images** button (left side of prompt bar)
3. Upload the existing asset from the project directory:
   - Use `browser_fill_form` or interact with the file upload dialog
   - Alternatively, if the image is accessible via URL, paste the URL directly
4. The uploaded image appears as a thumbnail in the prompt area
5. Add your text prompt after it

### As image prompt (composition + content influence)
Upload or reference the existing asset, then describe what you want:
```
[uploaded project screenshot] a new onboarding screen in the same visual style --iw 1.5 --ar 16:9
```
- `--iw 0.5-1`: Light influence -- picks up general vibe
- `--iw 1.5-2`: Strong influence -- closely follows layout, colors, feel
- `--iw 2.5-3`: Very strong -- almost reproduces the original with modifications

### As style reference only
If you want the aesthetic but completely different content:
```
a mountain landscape --sref [uploaded-asset-url] --sw 300
```
This extracts only the color palette, texture, and rendering style from your existing asset.

### Practical workflow for project assets
```
1. Read the project to find existing assets:
   - Glob for images: **/*.png, **/*.svg, **/*.jpg
   - Check public/, assets/, static/, images/ directories
   
2. Pick the most representative asset (hero image, key illustration, etc.)

3. Upload it to Midjourney as a style reference

4. Generate new assets using that reference + consistent parameters
```

## Method 3: Seed Pinning

Use `--seed` to anchor the random generation starting point. Same seed + similar prompt structure = similar aesthetic output.

```
minimalist icon, cloud upload, flat design, blue tones --seed 42 --ar 1:1 --s 300
minimalist icon, user profile, flat design, blue tones --seed 42 --ar 1:1 --s 300
minimalist icon, settings gear, flat design, blue tones --seed 42 --ar 1:1 --s 300
```

Seed alone gives moderate consistency. **Combine with --sref for best results.**

## Method 4: Prompt Template Strategy

Create a reusable prompt template with fixed style descriptors, varying only the subject:

```
Template: "[SUBJECT], [STYLE BLOCK] [PARAMS BLOCK]"

Style block (keep identical):
"clean vector illustration, soft gradient background, rounded shapes, 
warm pastel palette, subtle shadow, modern SaaS aesthetic"

Params block (keep identical):
"--ar 1:1 --s 400 --raw --no text words"
```

Then for each image, only swap the subject:
```
cloud storage icon, clean vector illustration, soft gradient background... --ar 1:1 --s 400 --raw
user authentication icon, clean vector illustration, soft gradient background... --ar 1:1 --s 400 --raw
data analytics icon, clean vector illustration, soft gradient background... --ar 1:1 --s 400 --raw
```

## Combining Methods (Maximum Consistency)

For the tightest consistency across a set, combine all approaches:

```
[subject description], [shared style block] --sref [anchor-image] --sw 300 --seed 42 --ar 1:1 --s 400 --raw
```

1. **--sref** locks the visual style
2. **--seed** anchors the generation
3. **Shared prompt structure** reinforces consistent language
4. **Fixed parameters** (--s, --ar, --raw) prevent per-image drift

## Workflow for Generating a Themed Asset Set

### Planning phase
1. Decide the set: what images do you need? (e.g., 6 feature icons, 4 hero illustrations)
2. Define the style: colors, mood, rendering approach, aspect ratio
3. Pick parameters: --s, --ar, --raw, --no, --chaos

### Anchor phase
4. Generate the first image with careful prompting until you're happy
5. Upscale it
6. Extract style reference via **Use > Style**

### Production phase
7. Generate remaining images using the style reference + consistent params
8. Use Draft mode for quick iteration, then re-run at full quality for keepers
9. Upscale all final selections with the same upscaler (Subtle or Creative, pick one)

### Quality control
10. Review all images together -- look for outliers in color, tone, or detail level
11. Re-generate any that drift too far, increasing --sw or --iw
12. Download the full set

## Moodboards for Ongoing Projects

For projects where you'll generate assets across multiple sessions:

1. Navigate to `/moodboards`
2. Create a moodboard for the project
3. Add your best/anchor images to it
4. In future sessions, reference the moodboard images as --sref sources
5. This preserves your style anchors across conversations

## Common Pitfalls

- **Don't change --stylize between images** -- different stylization levels create different rendering intensities
- **Don't mix --raw and non-raw** -- they produce fundamentally different aesthetics
- **Don't vary aspect ratio unnecessarily** -- different ratios change composition, which affects perceived style
- **Watch out for --chaos** -- any value above 0 introduces variation between grid images, making it harder to match across sets
- **Use Vary > Subtle, not Strong** when iterating -- Strong can drift from your established style
