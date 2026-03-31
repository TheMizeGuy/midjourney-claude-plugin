# Midjourney Plugin for Claude Code

Drive Midjourney's web UI via Playwright browser automation, using your existing subscription. No third-party APIs.

## Prerequisites

- **Playwright MCP plugin** installed in Claude Code
- **Active Midjourney subscription** (Pro plan recommended for stealth mode)
- **Logged in** to midjourney.com in the Playwright browser session

## Components

### Skills
- **midjourney-generate** - Step-by-step guide for generating images via Playwright (login, settings, prompting, waiting, upscaling, downloading)
- **midjourney-reference** - Complete parameter reference, prompt tips, aspect ratios, style references, model versions

### Agents
- **midjourney-creator** - Autonomous agent that handles the full generation workflow from description to downloaded file

## Installation

```bash
claude --plugin-dir /path/to/midjourney-plugin
```

Or add to your project's `.claude/plugins/` directory.

## Usage

Just ask Claude to generate an image:
- "make me a desktop wallpaper of a mountain sunset"
- "generate some abstract art in dark mode colors"
- "what does --sref do in midjourney?"

The plugin will automatically activate the relevant skill or agent based on your request.
