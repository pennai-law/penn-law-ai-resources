# PennChat Overview & Tutorial — Faculty Zoom Deck

**Date:** 2026-07-19
**Owner:** Polk Wagner
**Status:** Shipped 2026-07-20 as PR #9. See the plan's Outcome section for what changed.

## Goal

A 15–20 minute presentation introducing PennChat to Penn Carey Law faculty over
Zoom, roughly half slides and half live demo. The same artifact must work later
as a short recorded video without re-authoring.

## Audience

PCL faculty, mixed-to-fluent. Most have used ChatGPT; few have opened PennChat.
No "what is a chatbot" content.

## Deliverable

A self-contained HTML deck built with the `polk-slides` skill. Ten slides, in
that skill's standard Penn Carey Law theme — 16:9, Oswald, regular shield. The
deck does **not** adopt the portal's web typography (EB Garamond / DM Sans);
`polk-slides` has one theme and no style-selection step, and a deck is not a
web page.

Content source of record is `decks/pennchat-tutorial/pennchat-tutorial.md`
(the slidekit DSL); the HTML is a build artifact rendered from it.

**Location:** `pennchat-tutorial.html` at the root of this repo, linked from
`ai-office-hours.html`. Rationale: it is faculty-facing, matches the
`ai-office-hours.html` precedent, and inherits the portal's domain and
shortlink. The repo is public; all content is already-public faculty guidance.

## Key structural decision: demo cue slides

Four of the ten slides are cue cards. Each states the demo's goal, the exact
click path, and carries captured screenshots as a sub-sequence.

- **Live:** Polk glances at the cue, switches to the browser, demos.
- **Video:** Polk never leaves the deck; the screenshots carry the beat.

One artifact serves both delivery modes. This is the reason screenshots are
captured up front rather than skipped in favor of pure live demo.

## Running order

| # | Slide | Type | Time |
|---|-------|------|------|
| 1 | Title | Slide | 20s |
| 2 | What PennChat is — Penn-hosted LibreChat fronting Claude + GPT, free during the pilot, cleared for most University data (not SSNs, card data, or PHI) | Slide | 90s |
| 3 | How it compares — PennChat / ChatGPT EDU / Claude.ai | Slide | 60s |
| 4 | Log in, first real faculty task | **Demo** | 2m |
| 5 | Which model — start with Claude Sonnet 5; the three tiers; when to switch | Slide | 90s |
| 6 | Model picker, set your default | **Demo** | 90s |
| 7 | Upload a document, ask questions of it | **Demo** | 2m |
| 8 | Agents = custom GPTs — instructions + knowledge files + tools; rebuild, not migrate | Slide | 2m |
| 9 | Agent Builder, live | **Demo** | 3m |
| 10 | Where to go next — portal, PennChat FAQ, office hours | Slide | 30s |

Total: ≈7.5 min slides, ≈8.5 min demo.

### Content decisions

- **Copilot is out.** Slide 3 compares only the tools faculty actually choose
  between. (Reversed from an earlier draft that included it.)
- **Data rules get one line, not a slide.** Highest-consequence fact for
  faculty, but the portal covers it in depth; a line on slide 2 buys the
  protection without spending time.
- **Slide 9 is the flex.** If the clock passes 15 minutes, show a *pre-built*
  agent and narrate how it was made instead of building one. The cue slide
  carries both paths.

## Screenshot capture

Screenshots are captured from the live PennChat UI via Chrome automation before
any slide is built. No interface element is drawn or described that has not been
observed.

**Required frames:** login screen · a completed first chat · model picker
expanded (showing tiers) · default-model setting · document upload with a
response · Agent Builder (instructions, knowledge files, tools) · a finished
agent.

**Preconditions:** Polk logged into `pennchat.upenn.edu`, on PennNet/AirPennNet
or GlobalProtect.

**Privacy gate:** demos run in a scratch chat with non-sensitive content, and
Polk reviews every screenshot before it is committed to this public repo.

## Success criteria

1. Deck runs start to finish in 15–20 minutes with the demos.
2. Every UI claim traces to a captured screenshot.
3. Deck reads coherently with zero live demo — the video path works.
4. Voice is Polk's; faculty-only framing; no AI writing tells.

## Out of scope

- Copilot, Legora, and Harvey comparisons.
- Deep data-classification guidance (lives on the portal).
- Prompting technique instruction.
- Video recording and editing itself.
