# midjourney-claude-plugin

Standalone Claude Code plugin marketplace repo (single plugin: `midjourney`) that drives Midjourney's web UI through the Playwright MCP using the user's existing subscription — no third-party image API. Generate, upscale, edit (inpaint/outpaint/retexture), enforce cross-image consistency, download. Pure content repo: markdown agents/skills + JSON manifests; no build system, no tests, no CI.

## Structure

- `.claude-plugin/marketplace.json` — its OWN self-contained marketplace (not part of any umbrella marketplace; users add this repo directly or point `--plugin-dir` at `midjourney/`).
- `midjourney/agents/midjourney-creator.md` — the driving agent. Frontmatter pins `model: sonnet` — retained deliberately as executor-tier browser-driving work (mechanical Playwright operation, not judgment). If output quality ever disappoints, remove the pin so it inherits the session model; never pin a dated ID.
- `midjourney/skills/` — four skills: `midjourney-generate` (login → settings → prompt → wait → download), `midjourney-edit`, `midjourney-reference` (full parameter tables), `midjourney-consistency` (--sref/--oref/--seed techniques).

## Working on this repo

- Default branch is `master`, not `main` — check before pushing.
- Runtime dependencies are environmental, not coded: the Playwright MCP must be installed, and a logged-in midjourney.com browser session must exist. Neither is verifiable in this repo; the agent's prerequisite-check step handles absence gracefully. There is nothing to CI here.
- Versioning: bump BOTH `midjourney/.claude-plugin/plugin.json` and the entry in `.claude-plugin/marketplace.json` together.
- No `.gitignore` exists; do not introduce generated artifacts that would need one — downloads belong under the consuming session's cwd, never this repo.
- Skill edits follow the standard skill-authoring bar: trigger phrases live in each SKILL.md frontmatter `description`; keep them concrete ("generate an image of...", "upscale", "--sref") rather than generic.
