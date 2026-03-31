---
name: midjourney-reference
description: Use when the user asks about Midjourney parameters, settings, prompt syntax, aspect ratios, style references, model versions, or needs help crafting a Midjourney prompt. Also use when user asks "what does --sref do", "how do I use character reference", "midjourney tips", or similar questions about MJ features and capabilities.
---

# Midjourney Parameter & Feature Reference

Complete reference for all Midjourney parameters, prompt techniques, and features. Use this to craft effective prompts and advise on parameter choices.

## Quick Parameter Reference

| Parameter | Syntax | Range | Default | Purpose |
|-----------|--------|-------|---------|---------|
| --ar | --ar W:H | Any ratio | 1:1 | Aspect ratio |
| --stylize / --s | --s N | 0-1000 | 100 | Artistic interpretation strength |
| --chaos / --c | --c N | 0-100 | 0 | Variation between grid images |
| --weird / --w | --w N | 0-3000 | 0 | Unconventional/surreal qualities |
| --no | --no item1, item2 | Text | None | Exclude elements (negative prompt) |
| --raw | --raw | Flag | Off | Literal prompt, less MJ aesthetic |
| --seed | --seed N | 0-4294967295 | Random | Reproducibility |
| --tile | --tile | Flag | Off | Seamless tileable pattern |
| --stop | --stop N | 10-100 | 100 | Stop generation at percentage |
| --quality / --q | --q N | 1, 2, 4 | 1 | GPU time per image |
| --repeat / --r | --r N | 2-40 | 1 | Run prompt multiple times |
| --iw | --iw N | 0-3 | 1 | Image prompt weight vs text |
| --sref | --sref URL/code | URL or number | None | Style reference |
| --sw | --sw N | 0-1000 | 100 | Style reference weight |
| --cref | --cref URL | URL | None | Character reference (V6) |
| --cw | --cw N | 0-100 | 100 | Character reference weight |
| --oref | --oref URL | URL | None | Omni reference (V7+) |
| --ow | --ow N | 0-1000 | 100 | Omni reference weight |
| --p | --p | Flag | Off | Apply personalization profile |
| --exp | --exp N | 5-100 | Off | Experimental creative boost (V7+) |
| --v | --v N | 1-8 | 7 | Model version |
| --niji | --niji N | 5-7 | None | Anime/Eastern aesthetic model |

## Aspect Ratio Guide

Common ratios and their use cases:

| Ratio | Use Case |
|-------|----------|
| 1:1 | Square, social media, icons |
| 4:3 | Classic monitor, presentations |
| 3:2 | Photography, prints |
| 16:9 | Desktop wallpapers, widescreen |
| 21:9 | Ultrawide monitors (MJ normalizes to 7:3) |
| 9:16 | Mobile wallpapers, stories, vertical video |
| 2:3 | Portrait photography, posters |
| 3:4 | Tablets, book covers |

MJ auto-simplifies ratios: 1920:1080 becomes 16:9, 21:9 becomes 7:3. No decimals allowed (use 139:100 not 1.39:1).

## Parameter Deep Dives

### Stylization (--s)
- **0-50**: Very literal, close to prompt. Good for technical/architectural prompts.
- **100** (default): Balanced. Good starting point.
- **250-500**: Noticeably artistic. MJ adds its own aesthetic choices.
- **750-1000**: Highly stylized. MJ takes creative liberty. May drift from prompt.

### Weirdness (--w)
- **0** (default): Normal output.
- **50-250**: Subtle quirky touches. Interesting textures or unexpected compositions.
- **500-1000**: Distinctly unusual. Surreal elements emerge.
- **1000-3000**: Very experimental. Results can be unpredictable.

### Chaos/Variety (--c)
- **0** (default): All 4 grid images are similar interpretations.
- **10-30**: Moderate diversity. Good for exploring directions.
- **50-100**: High diversity. Each image may be radically different.

### Quality (--q)
- **1** (default): Standard quality. Best for most uses.
- **2**: More detail and texture. 2x GPU time.
- **4**: Maximum detail. 4x GPU time. Not compatible with --oref.

### Raw Mode (--raw)
Disables MJ's automatic aesthetic polish. Use when:
- You want photorealistic results with simple prompts
- You have a detailed stylistic prompt and want precise control
- You want less "MJ look" in the output

## Style References (--sref)

Apply visual styles from reference images or codes:

```
cyberpunk cityscape --sref https://example.com/neon-art.jpg
```

- Multiple refs: separate URLs with spaces
- Style codes: numeric codes from Style Creator (e.g., `--sref 6281858160`)
- Random: `--sref random` for surprise styles
- Weight: `--sw 0-1000` controls style strength (default 100)
- Combine with `--raw` for style + literal prompt control

## Character/Omni References

### V6: Character Reference (--cref)
```
portrait of a warrior --cref https://example.com/character.jpg --cw 80
```
- `--cw 100`: Full character (face, hair, clothing)
- `--cw 0`: Face only

### V7+: Omni Reference (--oref)
Replaces --cref with unified reference:
```
running through a forest --oref https://example.com/character.jpg --ow 200
```
- Higher `--ow` = stricter adherence to reference
- Competes with `--stylize` and `--exp` for influence

## Multi-Prompt Syntax (::)

Weight different concepts independently:
```
hot::2 dog     → emphasizes "hot" (temperature), less "dog"
hot dog        → the food item
```

- No space before `::`
- Default weight is 1
- Negative weights allowed: `plants::-0.5`
- All weights must sum to positive

## Negative Prompts (--no)

Exclude unwanted elements:
```
landscape photo of mountains --no clouds fog haze people
```

**Warning**: MJ reads each word independently. `--no modern clothing` is interpreted as "no modern" AND "no clothing" which can trigger moderation. Prefer describing what you WANT rather than excluding.

## Permutations

Generate multiple variations in one command:
```
a {red, blue, green} car in a {city, desert}
```
Creates 6 separate prompts (3 colors x 2 locations).

Works with parameters too:
```
sunset landscape --ar {16:9, 1:1, 9:16}
```

## Model Versions

| Version | Key Features |
|---------|-------------|
| **V7** (default) | Production model. --oref, --exp, --sref, --p support |
| **V8 Alpha** | 5x faster, --hd (native 2K), --q 4, better text. alpha.midjourney.com only |
| **V6.1/V6** | --cref support, beautiful results |
| **Niji 7** | Anime/Eastern aesthetics, better coherency |
| **Niji 6** | Japanese text rendering, anime eye detail |
| **Draft** | Fast iteration, lower quality. Toggle via UI or settings |

## Speed Modes

| Mode | Speed | Cost | Availability |
|------|-------|------|-------------|
| Relax | Slowest | Free (unlimited) | Standard+ plans |
| Fast | Normal (~60s) | 1x fast hours | All plans |
| Turbo | ~4x faster | 2x fast hours | V5+, NOT V8 Alpha |

## Post-Generation Actions

| Action | What it does |
|--------|-------------|
| Vary > Subtle | Minor changes to selected image |
| Vary > Strong | Significant changes |
| Upscale > Subtle | 2x resolution, preserves original look |
| Upscale > Creative | 2x resolution, may enhance/alter details |
| Rerun | Generate new set with same prompt |
| Edit | Open in editor for inpainting/outpainting |
| Use > Image | Use as image prompt for new generation |
| Use > Style | Extract style for new generation |
| Use > Prompt | Copy prompt for reuse |

## Prompt Crafting Tips

1. **Front-load important concepts** -- MJ weighs earlier words more heavily
2. **Be specific about style** -- "oil painting", "watercolor", "photograph", "3D render"
3. **Include lighting** -- "golden hour", "neon lighting", "dramatic shadows", "soft diffused light"
4. **Specify camera/lens** -- "wide angle", "macro", "telephoto", "35mm film"
5. **Use artist/style references** -- describe aesthetic qualities rather than naming artists
6. **Keep it concise** -- long prompts can dilute focus. 40-60 words is a sweet spot.
7. **Use --raw for control** -- if MJ is adding too much of its own aesthetic
8. **Iterate with Draft** -- use draft mode to quickly test prompt ideas before full generation
9. **Use --chaos for exploration** -- higher chaos shows more diverse interpretations
10. **Combine --sref with simple prompts** -- style refs work best when text describes content, not style

## Wallpaper-Specific Tips

- Use `--ar 16:9` for standard monitors, `--ar 21:9` for ultrawide
- Use `--stylize 500-800` for aesthetic wallpapers
- Always **Upscale > Subtle** for clean high-resolution output
- Add `--no text words logos watermark` to keep it clean
- Use `--raw` for photorealistic wallpapers
- Dark themes work well: "dark background", "moody atmosphere", "deep shadows"
