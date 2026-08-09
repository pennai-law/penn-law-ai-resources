# Penn Carey Law AI Resources — Status

**Updated:** 2026-08-09

The faculty AI portal is live at resources.pennai.law and deploys straight from `main` via GitHub Pages, with no build step. Content this cycle arrived from the other machine and the bi-weekly stale-check routine: PRs #15–#18 split the access table into full-time and adjunct faculty columns, qualified the research-account claims, noted that Zoom's AI features are off by default, enlarged the by-request glyphs so they read at a glance, and named the destination in the attribution-stripe CTA. This session added `AGENTS.md`, a Codex-facing counterpart to `CLAUDE.md`, and excluded it from the published site alongside the other repo notes.

**Where it stands:** Deployable and current. `main` is clean and level with origin. The portal keeps its own design — EB Garamond on cream — and shares only the Penn bar and the footer W2 lockup with pennai.law; that divergence is deliberate and documented in `CLAUDE.md`.

**Next:** Nothing queued. The stale-check routine surfaces link rot and outdated tool guidance on its own schedule.

**Open:** `CLAUDE.md` and `AGENTS.md` are now a generated pair. After editing `CLAUDE.md`, regenerate with `~/.claude/scripts/sync-agents-md` rather than hand-editing both — a global "Claude" → "Codex" replace silently breaks real filenames like `claude-skills.html` and the "Advanced Claude" tab label.
