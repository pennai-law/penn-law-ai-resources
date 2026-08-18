# Penn Carey Law AI Resources — Status

**Updated:** 2026-08-17

The faculty AI portal is live at resources.pennai.law and deploys straight from `main` via GitHub Pages, with no build step. Content this cycle arrived from the other machine and the bi-weekly stale-check routine: PRs #15–#18 split the access table into full-time and adjunct faculty columns, qualified the research-account claims, noted that Zoom's AI features are off by default, enlarged the by-request glyphs so they read at a glance, and named the destination in the attribution-stripe CTA. A prior session added `AGENTS.md`, a Codex-facing counterpart to `CLAUDE.md`, and excluded it from the published site alongside the other repo notes. This session made no content changes — it recorded the 2026-08-17 AI office hours and queued the portal gaps that session exposed.

**Where it stands:** Deployable and current. `main` is clean and level with origin. The portal keeps its own design — EB Garamond on cream — and shares only the Penn bar and the footer W2 lockup with pennai.law; that divergence is deliberate and documented in `CLAUDE.md`.

**Next:** Four items, all from live faculty questions at AI office hours on 2026-08-17 (full note: Box → `PCL AI Project/Lab/workstreams/01-lab-ops/meeting-summaries/ai-office-hours-2026-08-17.md`). These are observed navigation failures, not speculative additions — two faculty could not find things that are already on the site.

- **Skill discoverability.** Cathie Struve knew the MCQ skill existed and still asked "where's that?"; Allison Hoffman asked how she would find the published skills at all. `claude-skills.html` exists and is linked, but not from where people look. Surface it from Getting Started and from the Advanced Claude tab, not only from the toolkit link.
- **Penn Claude vs. personal Claude.** The comparison came up twice and is not on the site. The short version faculty need: no feature differences except **Cowork is absent from the Penn version**, no educational discount so no price advantage, and the only substantive difference is the Anthropic–Penn indemnification agreement — which is precisely what makes the Penn version the FERPA path. Whether skills/agents can be shared inside Penn Claude is still unknown; say so rather than implying it works.
- **FERPA guidance.** Worth a short block on the line as drawn live: student surveys and name/email lists alone are not FERPA; linkage to an educational record is the trigger; FERPA-covered material goes to Penn Claude.
- **Legora.** Contract was expected to be signed the week of Aug 17. Add to the access table once confirmed — do not list it as available before then.

The stale-check routine continues to surface link rot and outdated tool guidance on its own schedule.

**Open:** `CLAUDE.md` and `AGENTS.md` are now a generated pair. After editing `CLAUDE.md`, regenerate with `~/.claude/scripts/sync-agents-md` rather than hand-editing both — a global "Claude" → "Codex" replace silently breaks real filenames like `claude-skills.html` and the "Advanced Claude" tab label.
