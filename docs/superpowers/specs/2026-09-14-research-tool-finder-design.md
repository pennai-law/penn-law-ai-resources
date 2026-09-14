# Design: Research Tool Finder + AI for Research

**Date:** 2026-09-14
**File touched:** `index.html` (only)

## Goal

The router's "…work out which tool fits the task" link promises to answer which tool
fits the job. It doesn't — it lands on a box titled "Which model should I use?" that
only ever answers *which PennChat model*, never Harvey vs. Legora vs. Westlaw vs.
Claude.ai vs. an agentic tool. This closes that gap with a real task-based finder, plus
a companion section for research workflows specifically, which don't fit neatly into
"draft" or "review."

Prompted by reviewing DDDI's `ai-resources-penn-sas` site (built for SAS, explicitly
modeled on this portal), which has an interactive tool-finder this portal lacks.

## Decisions

**No new tab, no separate portal.** This portal's own charter is tools, access, and
policy; a task-based tool recommender is squarely "tools." A separate portal would
duplicate this site's own content against its explicit anti-duplication rule with
`pennai.law` and the pedagogy portal. Everything lands inside the existing **Using AI**
tab, next to the box it's meant to fix.

**Add, don't replace.** The existing "Which model should I use?" box still answers a
real, narrower question once someone is already inside PennChat. The Tool Finder goes
above it, not instead of it. (Polk's call, 2026-09-14 — the sketch had proposed
replacing it.)

**The Tool Finder doesn't duplicate the tool catalog.** Every tool it recommends
already has a card under Getting Started → "AI Tools at Penn Law" (General AI Tools,
Legal-Specific Tools) or Agentic & Advanced. The finder is a task-based lens on top of
that catalog, deep-linking to existing cards via the already-defined `goToCard(tab,
cardId)` helper (`index.html:2453`) — it never re-describes a tool the catalog already
covers.

**AI for Research is organized by research verb, not by vendor.** Find it / Verify it
/ Synthesize it / Analyze it — the same tools as the catalog, reframed around the shape
of a research task. This mirrors how "Which model should I use?" already reframes
PennChat's model list around task, rather than introducing a new taxonomy.

**Adding a CourtListener card.** Free, public, run by the Free Law Project (a 501(c)(3)
nonprofit) — case law, judges, oral arguments, and RECAP (crowdsourced federal
dockets), with a public REST API. It has no card today, so the Tool Finder's "Find it"
research result had nothing real to link to for public case-law search specifically.
Polk's call, 2026-09-14 — the sketch had left this as a caveat rather than a real
addition.

**No `card-badge` risk tier invented for CourtListener.** It isn't a Penn-licensed
tool, so it doesn't have a Penn data-risk determination the way Harvey or Legora do.
Rather than assign a risk badge, the card says plainly that none has been done, since
it's a public-records search tool, not a place Penn material gets sent.

## Changes

### 1. Tool Finder (`index.html`, before `id="pennchat-models"` at `:1909`)

New CSS section `/* ── TOOL FINDER ── */` and `/* ── AI FOR RESEARCH ── */`, inserted
into the shared `<style>` block after the "CALLOUT / HIGHLIGHT BOX" section (`:841`).
New markup: a `.finder` box (same visual language as the file's existing callout boxes:
navy left border, cream ground, badge-pill + `<h3>` header) containing two `<fieldset>`
chip-groups (task, data sensitivity) and a results list. New JS block, inserted
immediately after `goToTab()` (`:2468`), holding the tool → task/risk data and the
render function. No `:has()` CSS selector — active-chip state is toggled by a JS class
(`chip-active`), matching how `.tab-btn.active` already works elsewhere in the file,
rather than introducing a selector this codebase doesn't otherwise use.

### 2. AI for Research (`index.html`, immediately after the Tool Finder)

Four `.verb-row` blocks (Find it / Verify it / Synthesize it / Analyze it), each a
short paragraph plus links to existing cards via `goToCard`. No new tool descriptions —
only pointers.

### 3. CourtListener card (`index.html`, Legal-Specific Tools grid, Getting Started tab)

One new `.card` appended after Lexis+ AI, before the grid's closing `</div>`
(`:1450`ish). Uses the existing-but-currently-unused `.card-icon` text-badge variant
("CL" on navy) rather than sourcing a new logo asset — a reasonable placeholder; a real
CourtListener logo is a trivial follow-up if Polk wants one.

The section's intro line (`:1381`) currently reads "Built specifically for legal work
— trained on legal data and designed for legal research and drafting," which is
accurate for Harvey/Legora/Westlaw/Lexis but wrong for CourtListener (a database, not a
trained model). Widen the sentence rather than misdescribe the new card.

### 4. Re-anchor `#which-model` to the new Tool Finder

The router's existing link (`:1113`, `href="#which-model"`) and the "Model Picker ↓"
jump link (`:1833`) already point at the *concept* this feature now owns. Rather than
edit those hrefs, move the `id="which-model"` anchor itself: the old PennChat-model
heading becomes `id="which-pennchat-model"`, and the new Tool Finder heading takes over
`id="which-model"`. Update the section-heading `scroll-margin-top` selector list
(`:513`) to include `#which-pennchat-model`. Update the two link labels ("Which model
should I use?" → "Find the right tool for the task"; "Model Picker ↓" → "Tool Finder
↓") so the visible text matches what's actually there. No other file references
`#which-model` as a fragment (checked — the only other `which-model*` id is
`#which-model-claude`, a distinct anchor, untouched).

## Assumptions to verify before publishing

Approved on stated assumptions, not confirmed institutional fact. These are read
straight off existing card badges plus one interpretive call; if any is wrong, only the
Tool Finder's `FINDER_TOOLS` data object changes:

- **PennChat** — Low/Moderate/High, from its card's `badge-risk-high` "Approved: High,
  Mod & Low."
- **Claude.ai (Enterprise)** — Low/Moderate only, from `badge-risk-mod` "Approved: Low
  & Mod (Enterprise)." Not offered for High Risk in the finder.
- **Harvey, Legora** — Low/Moderate, from their `badge-risk-mod` badges.
- **Westlaw AI-Assisted Research, Lexis+ AI — treated as available at all three risk
  levels.** Their actual badge is `badge-risk-sub` "Legal Research Terms," a different
  category from Penn's Low/Moderate/High tiers entirely (governed by the vendor
  subscription, not Penn's AI risk classification). The finder's risk filter maps
  "Legal Research Terms" tools to "always shows," which is an interpretive call, not a
  fact read off a badge. If that's wrong, these two should instead be tagged to a
  specific risk tier or excluded from the risk filter altogether.
- **Claude Code, the Penn LLM Gateway** — assumed Low/Moderate. Neither carries a
  card-badge risk tier today (Claude Code's card has no `.card-badges` block at all);
  this rides on the parent Claude Enterprise approval rather than a stated fact.
- **CourtListener — no risk tier assigned, by design** (see Decisions above), not an
  oversight to fix.

## Post-ship revision: Yes/No sensitivity toggle (Task 6)

**Requested by Polk after PR #45 was open and reviewed clean.** The Low/Moderate/High
selector above never did three-way work — every `FINDER_TOOLS` entry was tagged either
`['low','moderate']` or `['low','moderate','high']`; nothing ever distinguished Low
from Moderate on its own. Replaced with a single Yes/No "highly sensitive Penn data
involved?" toggle backed by a boolean `sensitiveOk` field, defaulting to "No" (the
common case, vs. the old default of "Moderate"). "No" shows the full roster for a task
regardless of the flag — a tool cleared for sensitive data is obviously fine for
non-sensitive data too; "Yes" filters to `sensitiveOk: true` tools only.

The assumptions above still hold, just relabeled: every tool the original design
tagged with `'high'` in its array is now `sensitiveOk: true` (PennChat, Westlaw,
Lexis+, CourtListener); everything tagged only `['low','moderate']` is now
`sensitiveOk: false` (Claude.ai, Harvey, Legora, Claude Code, the Penn LLM Gateway).
The interpretive calls — Westlaw/Lexis+'s "Legal Research Terms" badge mapped to
"always cleared," Claude Code/LLM Gateway's assumed tier, CourtListener's deliberate
non-participation in Penn's risk framework — are unchanged in substance, just
expressed as `true` instead of an array containing `'high'`.

## Out of scope

- No change to any existing tool card's content, only the one intro-sentence edit
  noted above and the id rename.
- No restyling, no new tab, no change to the tab structure.
- Not sourcing an official CourtListener logo asset — the text-badge placeholder is
  intentional for this PR.
- Not auditing or correcting the risk badges already on Harvey/Legora/Westlaw/Lexis/
  PennChat/Claude.ai — this PR reads them as given.
