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

`_config.yml` excludes `CLAUDE.md`/`AGENTS.md`/`README.md`/`STATUS.md` from the published site (Jekyll), so these notes stay in the repo but off the live site.

## Structure

- `index.html` — the whole portal: a six-tab layout (Getting Started · Using AI · Agentic AI · Advanced Claude · Policies · AI at Penn) with inline CSS/JS and a Cmd+K site search. Self-contained. Tab display names differ from internal ids kept stable for links: `use-cases` (Using AI), `tools` (Advanced Claude), `policies` (Policies). Any element with an `id` inside a pane is deep-linkable — `activateFromHash` resolves the owning tab via `closest('.tab-pane')`, so a new anchor works in a shared URL with no routing table to update.
  - **The "I want to…" router sits above the tab bar, outside every pane**, so it shows on all six tabs and is not Getting Started content. It replaced a six-column per-tab overview map that duplicated the tab bar directly beneath it. Because it lives outside `.page-body`, it carries the hero block's 1100px measure and gutters rather than the page body's; without those it runs edge to edge.
  - The tab bar is the primary navigation now that the overview map is gone, and is styled for that — 15px/600 labels, a 58px band, a navy bottom rule. Its row scrolls horizontally under ~880px, with edge fades toggled by scroll position and re-measured on `document.fonts.ready` (the labels are DM Sans, so a pre-font measurement is wrong).
- `agentic-ai-security.html` — long-form security guide for faculty use of agentic tools (linked from the portal).
- `agentic-ai-overview.html` — redirect stub → `index.html#agentic-ai` (kept for old inbound links).
- `claude-skills.html` — catalog of public faculty document and review skills, with current product eligibility notes. Teaching and assessment skills are handed off to the Pedagogy Resources portal rather than described here.
- `ai-office-hours.html` — faculty AI office-hours session screen. **Deliberately unlinked from the site** since 2026-08-30: it is opened directly and projected during a session, not browsed to. It still builds and serves.
- `ai-signage.html` — auto-cycling digital signage for the faculty lounge.
- `license.html` — license page (CC BY 4.0 content + Apache 2.0 code). The complete terms are in `LICENSE` and `LICENSE-CODE`; `LICENSE-NOTICE` maps the licenses when an HTML file contains both prose and code.
- `colophon.html` — how the site is written, hosted, and published, including an actual verbatim prompt. Built on `license.html`'s chrome. Linked from the footer, not the tab bar, so it adds nothing to navigation. The pedagogy portal has a matching one.
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
- **This portal covers tools, access, and policy. Teaching belongs to the pedagogy portal, and Project projects belong to `pennai.law`.** Both sibling sites already say so in this site's favour, so a duplicate here is this site breaking its own rule. Twice on 2026-08-30 the same failure was found and fixed: five teaching skills described in full on both this site and the pedagogy portal, with display names already drifted apart; and eleven Project cards duplicating `pennai.law/projects/`, which is a complete filterable index. When something reads as teaching guidance or a Project inventory, link it — don't restate it.
- **Link the Rebrandly shortlinks, not the destination hosts.** The pedagogy portal moved from `polkwagner.github.io` to `pedagogy.pennlawteaching.org` and left seven stale links here. `pennlaw.link/pedagogy` survives the next move; a direct host link does not.
- **Model-lineup facts come from four Anthropic pages, and plan rules are not lineup rules.** The models overview (`platform.claude.com/docs/en/about-claude/models/overview`) gives the current and legacy lists, latency, and API prices; `anthropic.com/claude/fable` gives which Claude.ai plans can use Fable and the safeguard routing; the help-center article "Codex Fable models on your plan" gives what each plan actually includes (Pro: usage credits only; Max: half of weekly usage); and "Why Codex switched models" gives the fallback behaviour (cyber → Opus 4.8, biology → Opus 5, with a notice). On 2026-09-03 the first draft of the `#claude-models` box was wrong on all three of the last points because it was written from the first page alone.
- **PennChat lags Anthropic's lineup by more than a month, and its picker is only checkable on Penn's network or GlobalProtect.** Six weeks after Opus 5 shipped it was still absent (checked 2026-09-03: Opus 4.8, 4.7, Sonnet 5, 4.6, Haiku 4.5; GPT 5.4, 5.1 and Minis). Never add a new Anthropic model to the PennChat table on the strength of Anthropic's release; check the picker's search box, and date the check in the Opus row and footnote.
- **Stacked PRs: after the base PR merges, wait for GitHub to retarget the stacked one to `main` (or retarget it yourself) before merging it.** On 2026-09-03 #27 was merged seconds after #26 and landed on the deleted feature branch instead of `main`; it had to be re-landed as #28.
- **Verify against the deployed site, not the local preview.** The in-app preview serves a `data:` snapshot: scripts run, but `location.hash` assignment is swallowed (so `hashchange` never fires) and programmatic `scrollLeft` sticks while dispatching no `scroll` event. Both made working features look broken on 2026-08-30. Anything driven by a hash or a scroll listener has to be checked against `resources.pennai.law`.

## History note

Migrated 2026-05-30 from a personal project (`polkwagner/penn-law-ai-resources`, served at `polkwagner.github.io/...`) to the `ai-teaching-lab` org and the `ai-resources.ai-teaching-lab.org` subdomain. Git history was purged of the prior `CLAUDE.md` and `docs/` before the repo went public.

Rebranded 2026-07-29. The AI Teaching Lab became the **Penn Carey Law AI Project**, the org was renamed `ai-teaching-lab` → `pennai-law`, and the portal moved to `resources.pennai.law`. The old subdomain redirects via the `ai-resources-redirect` repo, which preserves path **and fragment** so the PennChat deep links keep resolving; it must not be retired. Earlier names are deliberately preserved where they are historically accurate — the AI Law Lab Boot Camp, and the alumni credits in the Lab site's Toolkit.
