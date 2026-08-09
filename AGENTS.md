# AGENTS.md

Guidance for Codex (and the Lab team) working in this repository.

## What this is

The **Penn Carey Law AI Resources portal** — a faculty-facing hub for AI tools, use cases, policies, agentic-AI guidance, and the broader Penn AI ecosystem. It is a project of the **Penn Carey Law AI Project** (`github.com/pennai-law`), and is featured as a card in the Lab site's Toolkit.

- **Live:** https://resources.pennai.law/
- **Shortlink:** pennlaw.link/ai-resources (managed on Rebrandly)
- **Repo:** `pennai-law/penn-law-ai-resources` (public)
- **Audience:** Penn Carey Law faculty. (Staff guidance is handled separately by Penn Law ITS.)
- **Voice:** Polk Wagner's. Keep edits in his voice; faculty-only framing.

## Deploy

**No build step.** Static HTML site served by **GitHub Pages, "Deploy from a branch"** (`main`, root `/`). Push to `main` → live in a minute or two. The custom domain is set by the root `CNAME` file (`resources.pennai.law`); don't delete it. Preview locally by opening `index.html` in a browser.

`_config.yml` excludes `CLAUDE.md`/`AGENTS.md`/`README.md` from the published site (Jekyll), so these notes stay in the repo but off the live site.

## Structure

- `index.html` — the whole portal: a six-tab layout (Getting Started · Using AI · Agentic AI · Advanced Claude · Policies · AI at Penn) with inline CSS/JS and a Cmd+K site search. Self-contained. Tab display names differ from internal ids kept stable for links: `use-cases` (Using AI), `tools` (Advanced Claude), `policies` (Policies). Any element with an `id` inside a pane is deep-linkable — `activateFromHash` resolves the owning tab via `closest('.tab-pane')`, so a new anchor works in a shared URL with no routing table to update.
- `agentic-ai-security.html` — long-form security guide for faculty use of agentic tools (linked from the portal).
- `agentic-ai-overview.html` — redirect stub → `index.html#agentic-ai` (kept for old inbound links).
- `claude-skills.html` — listing of the public AI skills.
- `ai-office-hours.html` — faculty AI office-hours session screen.
- `ai-signage.html` — auto-cycling digital signage for the faculty lounge.
- `license.html` — license page (CC BY 4.0 content + Apache 2.0 code).
- `assets/` — favicons, `logos/` (brand-tool icons), the Lab stacked SVG.

## Design system

Penn navy (`#011F5B`), red (`#990000`), cream (`#FAF8F3`), gold (`#B8960C`); EB Garamond (headings) + DM Sans (body), via Google Fonts (the only external dependency).

**The portal keeps its own design, with two deliberate exceptions.** It is a distinct site and is *not* restyled to match `pennai.law` — the Project site is Rajdhani + IBM Plex on white, and converting the portal would mean rewriting inline CSS across eight standalone files to no benefit. The portal is read at length, and EB Garamond suits that better than a condensed display face. The cream ground and the gold are the portal's own and signal "reference material" against the Project site's white; keep them.

What *is* shared, on purpose, so the two read as siblings:

- **The Penn bar** — the same 4px navy/red stripe opens every page that has site chrome (`index`, `agentic-ai-security`, `claude-skills`, `license`). `ai-office-hours` and `ai-signage` are chrome-less by design; do not add it there.
- **The W2 lockup**, in the footer only, at `assets/pennai-lockup-white.svg`. It must render **at least 288px wide**: the mark carries `pennai.law` inside it, which drops below 8px and stops being legible under that, and 288px is 75px tall — too heavy for the header or the attribution stripe, which is why it lives in the footer. Below a 400px viewport it is hidden rather than shrunk; the top stripe still carries the attribution as text. Colour version is `assets/pennai-lockup.svg`; regenerate both from `brand/` in the `ai-teaching-lab.org` repo, never by hand.

## Conventions

- **Branch + PR** for substantive changes; keep `main` deployable.
- **Don't expose dev scaffolding.** The detailed design history (original design docs, plans, the Eddie content review, signage specs) is archived in Box at `AI Teaching Lab/Lab/website/penn-law-ai-resources-dev-docs/`, intentionally **not** in this public repo. Keep new internal-only notes out of the published tree (add them to `_config.yml`'s `exclude:` if they must live here).
- **Names / facts:** verify people and claims against authoritative sources; never fabricate. The Lab people registry is `AI Teaching Lab/Lab/team/NAMES.md` in Box.

## History note

Migrated 2026-05-30 from a personal project (`polkwagner/penn-law-ai-resources`, served at `polkwagner.github.io/...`) to the `ai-teaching-lab` org and the `ai-resources.ai-teaching-lab.org` subdomain. Git history was purged of the prior `CLAUDE.md` and `docs/` before the repo went public.

Rebranded 2026-07-29. The AI Teaching Lab became the **Penn Carey Law AI Project**, the org was renamed `ai-teaching-lab` → `pennai-law`, and the portal moved to `resources.pennai.law`. The old subdomain redirects via the `ai-resources-redirect` repo, which preserves path **and fragment** so the PennChat deep links keep resolving; it must not be retired. Earlier names are deliberately preserved where they are historically accurate — the AI Law Lab Boot Camp, and the alumni credits in the Lab site's Toolkit.
