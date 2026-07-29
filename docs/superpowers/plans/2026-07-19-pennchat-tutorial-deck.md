# PennChat Faculty Tutorial Deck Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a ten-slide, self-contained HTML deck introducing PennChat to Penn Carey Law faculty in a 15–20 minute Zoom session, half slides and half live demo, re-usable as a short recorded video.

**Architecture:** Content is authored in the slidekit markdown DSL at `decks/pennchat-tutorial/pennchat-tutorial.md`; `slidekit.py` renders the self-contained HTML next to it. Four of the ten slides are `img-explain` "demo cue" slides carrying real PennChat screenshots, so the deck works both as a live-demo backdrop and as a standalone video. The rendered HTML is copied to the repo root as `pennchat-tutorial.html` and linked from `ai-office-hours.html`.

**Tech Stack:** `polk-slides` skill (slidekit DSL + `scripts/slidekit.py`), Chrome automation via `mcp__claude-in-chrome__*` for screenshot capture, GitHub Pages static hosting.

## Global Constraints

- **Deck theme:** `polk-slides` standard Penn Carey Law theme, `shield: reg`, 16:9. No style selection, no hand-edited HTML, ever.
- **Content source of record:** `decks/pennchat-tutorial/pennchat-tutorial.md`. All prose edits go here, followed by a re-render. Never edit the HTML for content.
- **Render command:** `python3 ~/.claude/skills/polk-slides/scripts/slidekit.py decks/pennchat-tutorial/pennchat-tutorial.md`
- **No invented UI.** Every claim about the PennChat interface must trace to a screenshot captured in Task 1. If a frame was not captured, the corresponding slide claim is cut, not guessed.
- **Screenshots are reviewed by Polk before commit.** Public repo. Demo content must be non-sensitive.
- **Voice:** Polk Wagner's — direct, active, faculty-to-faculty. Observe the banned-phrase and AI-tell lists in `~/.claude/CLAUDE.md`.
- **Copilot, Legora, and Harvey are out of scope.** Slide 3 compares PennChat, ChatGPT EDU, and Claude.ai only.
- **Total runtime target:** 15–20 minutes, ≈7.5 min slides and ≈8.5 min demo.
- **Screenshots go in** `decks/pennchat-tutorial/assets/`, referenced by relative path from the md.

### Verified facts from the live UI (2026-07-19) — use these verbatim

These were observed in Task 1 and override both this plan's earlier drafts and
the portal's current copy.

- **Models available:** Claude Sonnet 5, Claude Sonnet 4.6, Claude Opus 4.8, Claude Haiku 4.5, GPT-5.4. In the agent builder these appear as raw ids (`us.anthropic.claude-sonnet-5`).
- **Picker groups:** My Agents · Azure OpenAI · PennChat · **Premium Models · Balanced Models · Economical Models · Legacy Models**. Four tiers, not three — the portal omits Legacy.
- **Providers** (agent builder): PennChat and Azure OpenAI.
- **"Set as default" is NOT verified.** No such control exists in Settings → General or Settings → Chat, and hovering a non-default model offers only a **pin**. Slide 5 and the model-picker demo say *pin*, never *set as default*. Do not reintroduce the default claim.
- **Agent ordering gotcha:** knowledge files cannot be attached until the agent is created. "Upload File Context" is disabled until then. The demo click path is *fill the form → Create → attach files → Save*.
- **Also present, unmentioned by the portal:** Projects, MCP Settings (Document_Generator and PDF_Generator servers), a visible usage-credit balance, and a ~887K-token context window. Out of scope for this deck; a follow-up portal PR should cover them.
- **Data banner, verbatim:** approved for High Risk Data, excluding Social Security and credit card numbers; avoid identifiable and protected health information including names and Medical Record Numbers.
- **Platform:** LibreChat v0.8.7.
- **Switching models clears an attached file.** Attach after choosing the model, not before.
- **Long documents are slow.** A 28-page PDF adversarial read did not return within 2.5 minutes. Demo 1 is pre-run before the session, never performed live.

## File Structure

| Path | Responsibility |
|---|---|
| `decks/pennchat-tutorial/pennchat-tutorial.md` | Deck content, source of record |
| `decks/pennchat-tutorial/assets/*.png` | Captured PennChat screenshots |
| `decks/pennchat-tutorial/pennchat-tutorial.html` | Build artifact from slidekit |
| `pennchat-tutorial.html` | Published copy at repo root |
| `ai-office-hours.html` | Modified: adds a link to the deck |
| `_config.yml` | Modified: excludes `decks/` from the published site |

---

### Task 1: Capture the PennChat screenshots

**This task requires Polk present and logged in.** Do not start it unattended.

**Files:**
- Create: `decks/pennchat-tutorial/assets/01-login.png`
- Create: `decks/pennchat-tutorial/assets/02-first-chat.png`
- Create: `decks/pennchat-tutorial/assets/03-model-picker.png`
- Create: `decks/pennchat-tutorial/assets/04-default-model.png`
- Create: `decks/pennchat-tutorial/assets/05-agent-in-use.png`
- Create: `decks/pennchat-tutorial/assets/06-agent-builder.png`
- Create: `decks/pennchat-tutorial/assets/07-finished-agent.png`

**Interfaces:**
- Produces: the seven PNG paths above. Filenames are fixed — later tasks reference them literally.
- Four of the seven (`02`, `03`, `05`, `06`) appear on the deck's cue slides. The other three (`01-login`, `04-default-model`, `07-finished-agent`) are captured deliberately as spares: they are the frames the *video* cut will need when there is no live demo to fill those beats, and re-capturing later means getting back on VPN and rebuilding the demo state. Capture all seven now.

- [ ] **Step 1: Confirm preconditions with Polk**

Ask, and wait for an affirmative answer:
> "Are you logged into pennchat.upenn.edu and on PennNet/AirPennNet or GlobalProtect? I'll be driving your Chrome and capturing screenshots. Everything I type will go in a throwaway chat."

Do not proceed on silence or a maybe.

- [ ] **Step 2: Load the Chrome tools in a single call**

Run ToolSearch once with:
`select:mcp__claude-in-chrome__tabs_context_mcp,mcp__claude-in-chrome__navigate,mcp__claude-in-chrome__computer,mcp__claude-in-chrome__read_page,mcp__claude-in-chrome__tabs_create_mcp,mcp__claude-in-chrome__form_input`

- [ ] **Step 3: Get tab context, then open PennChat in a new tab**

Call `tabs_context_mcp` first. Do not reuse a tab ID from any earlier session. Then `tabs_create_mcp` and `navigate` to `https://pennchat.upenn.edu`.

- [ ] **Step 4: Capture the login/landing frame**

Screenshot via `computer`. Save as `assets/01-login.png`.

If Polk is already logged in and no login screen appears, capture the empty new-chat screen instead and note the substitution — do not log out to force the frame.

- [ ] **Step 5: Run the first-task demo and capture it**

In a new chat, submit this exact non-sensitive prompt:

```
I'm drafting a memo to faculty about a new course scheduling policy.
Here's my opening paragraph — tighten it without losing the detail:

"The Committee has determined, after review of several semesters of
enrollment data, that the current scheduling of upper-level electives
creates avoidable conflicts for students pursuing certificate programs."
```

Wait for the full response, then screenshot as `assets/02-first-chat.png`.

- [ ] **Step 6: Capture the model picker, expanded**

Open the model picker. Screenshot showing the Premium / Balanced / Economical tiers as `assets/03-model-picker.png`.

Record verbatim, in your notes for Task 2, the exact model names visible in each tier. Slide 5 uses these and must not paraphrase them.

- [ ] **Step 7: Capture the default-model setting**

Navigate to wherever the default model is set. Screenshot as `assets/04-default-model.png`. Record the exact click path — Task 3's cue slide prints it.

- [ ] **Step 8: Capture a document upload with a response**

Upload a non-sensitive PDF (a public syllabus or a published paper — ask Polk which). Ask a question of it, wait for the answer, screenshot as `assets/05-doc-upload.png`.

- [ ] **Step 9: Capture Agent Builder and a finished agent**

Open Agent Builder from the side panel. Screenshot the builder showing instructions, knowledge files, and tools as `assets/06-agent-builder.png`.

Build a small agent — suggested: "Syllabus Reviewer," instructions "You review law school syllabi for clarity and workload balance," one knowledge file. Screenshot the finished agent as `assets/07-finished-agent.png`.

- [ ] **Step 10: Stop on trouble**

If any tool call fails three times, or a page will not load, or an element will not respond: stop, tell Polk exactly what you tried and what happened, and ask how to proceed. Do not keep retrying and do not wander the site.

- [ ] **Step 11: Show Polk every screenshot and get sign-off**

Run: `~/.claude/scripts/show-image decks/pennchat-tutorial/assets/*.png`

Ask: "Any of these you don't want in a public repo?" Delete or re-capture anything he flags. Do not commit until he answers.

- [ ] **Step 12: Commit**

```bash
git add decks/pennchat-tutorial/assets
git commit -m "Add captured PennChat UI screenshots for the faculty deck"
```

---

### Task 2: Author the six talk slides

**Files:**
- Create: `decks/pennchat-tutorial/pennchat-tutorial.md`

**Interfaces:**
- Consumes: the verbatim model-tier names recorded in Task 1, Step 6.
- Produces: a `pennchat-tutorial.md` containing the `## deck` block and slides 1, 2, 3, 5, 8, 10. Task 3 inserts the four demo slides into this same file at the documented positions.

- [ ] **Step 1: Read the DSL grammar before writing a line**

Read `~/.claude/skills/polk-slides/references/markdown-deck-format.md` in full. The block types used here are `deck`, `title`, `bullets`, `table`, `icon-grid`, and `closing`.

- [ ] **Step 2: Write the file**

Create `decks/pennchat-tutorial/pennchat-tutorial.md`:

```
## deck
title: PennChat for Faculty
shield: reg

## title
title: PennChat
subtitle: What it is, and how to actually use it
author: R. Polk Wagner
date: Fall 2026

## bullets
title: What PennChat Is
- Penn's own AI chat portal, hosted by the University
- One interface in front of two model families: Anthropic's Claude and OpenAI's GPT
- Free to you during the pilot
- Cleared for Low, Moderate, and most High Risk University data, not SSNs, card data, or identifiable PHI
- Requires PennNet, AirPennNet, or GlobalProtect
callout: This is the safe default for Penn work.

## table
title: How It Compares
| | PennChat | ChatGPT EDU | Claude.ai |
| Who runs it | Penn | OpenAI, under Penn's agreement | Anthropic |
| Penn data cleared | Low, Moderate, most High | Low and Moderate | Not reviewed for Penn data |
| Models | Claude and GPT | GPT only | Claude only |
| Cost to you | Free during pilot | Free | Free tier, or personal paid |
commentary: When the work touches Penn data, use PennChat.

## bullets
title: Which Model to Pick
- Start with Claude Sonnet 5, and pin it so it sits at the top of the picker
- It handles nearly all faculty work well
- The picker groups models into Premium, Balanced, Economical, and Legacy
- Model names change as new versions ship; the tiers do not
- Premium models draw more of your usage credits
callout: Pick the newest model in the tier that fits the task.

## bullets
title: Agents Are Custom GPTs
- An agent is a chat you have set up in advance:
  - instructions it always follows
  - knowledge files it can search
  - tools, including web search and a code interpreter
- There is no one-click import from ChatGPT
- A custom GPT is rebuilt, not migrated: paste the instructions, re-upload the files
- Every custom-GPT capability has a direct equivalent
callout: Open Agent Builder from the side panel.

## closing
tagline:
- PennChat is where Penn work belongs.
- Start with one real task this week.
qr-label: AI Resources
qr-url: pennlaw.link/ai-resources
```

- [ ] **Step 3: Reconcile slide 5 against the captured picker**

Open `assets/03-model-picker.png`. If the tier labels are not literally "Premium," "Balanced," and "Economical," or Claude Sonnet 5 is not present, edit slide 5 to match what the screenshot shows. The screenshot wins over this plan.

- [ ] **Step 4: Render and confirm it parses**

```bash
cd "/Users/polk/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
python3 ~/.claude/skills/polk-slides/scripts/slidekit.py decks/pennchat-tutorial/pennchat-tutorial.md
```

Expected: writes `decks/pennchat-tutorial/pennchat-tutorial.html`, no error. A missing required field or a short table row is a hard error naming the block — fix and re-run.

- [ ] **Step 5: Commit**

```bash
git add decks/pennchat-tutorial/pennchat-tutorial.md decks/pennchat-tutorial/pennchat-tutorial.html
git commit -m "Add the six talk slides for the PennChat deck"
```

---

### Task 3: Add the four demo cue slides

**Files:**
- Modify: `decks/pennchat-tutorial/pennchat-tutorial.md`

**Interfaces:**
- Consumes: the seven PNGs from Task 1; the md from Task 2.
- Produces: the complete ten-slide md in final running order.

Each cue slide is an `img-explain`: the screenshot carries the beat for the video, and the bullets are the click path Polk follows live.

- [ ] **Step 1: Insert Demo 1 after the "How It Compares" table**

```
## img-explain
title: Demo — Let It Argue With You
image: assets/02-paper-upload.png
filename: A working draft, and a hostile referee
- Attach a draft you are actually working on
- Ask it to read as a hostile referee and to say what would fix each problem
- Run this before the session and leave the tab open, a 28-page read takes over two minutes
- The point is not that it flatters you
```

**Pre-run, do not perform live.** A 28-page adversarial read took over two
minutes to return with no visible output — a third of the session's demo budget
spent on a loading dot. Polk sends the prompt before the session and switches to
the finished tab. The third bullet is his own reminder.

- [ ] **Step 2: Insert Demo 2 after the "Which Model to Pick" slide**

```
## img-explain
title: Demo — The Model Picker
image: assets/03-model-picker.png
filename: Model picker, expanded
- Open the picker at the top of the chat
- Premium, Balanced, Economical, Legacy
- Pin Claude Sonnet 5 so it is always the first thing you see
```

Do not write "set as default" — see the verified-facts block in Global Constraints.

- [ ] **Step 3: Insert Demo 3 after the "Agents Are Custom GPTs" slide**

The standalone document-upload demo was merged into the agent demo: the TA agent
answers from its attached patents, so it carries the document beat too. Three
demos, not four.

```
## img-explain
title: Demo — Build a Virtual TA
image: assets/06-agent-builder.png
filename: Agent Builder
- Side panel, then Agent Builder, then Create New Agent
- Fill in the instructions and pick a model, then Create
- Only now can you attach knowledge files, then Save
- Short on time? Show the finished TA instead and narrate how it was made
```

The third bullet is the ordering gotcha; the fourth is the flex path. Both stay
on the slide as Polk's own reminders.

- [ ] **Step 4: Insert the payoff slide immediately after Demo 3**

```
## img-explain
title: What It Looks Like
image: assets/05-agent-in-use.png
filename: Patent Law TA — Week 3: Novelty & Priority
- A student asks whether a claim is anticipated
- The TA refuses to answer first, and asks what the student thinks
- It quotes § 102(a)(1) and points at Claim 1 of the attached patent
- Instructions and knowledge files, doing exactly what you told them to
```

- [ ] **Step 5: Verify the running order**

```bash
grep -n '^## ' decks/pennchat-tutorial/pennchat-tutorial.md
```

Expected, in this order: `deck`, `title`, `bullets`, `table`, `img-explain`, `bullets`, `img-explain`, `bullets`, `img-explain`, `img-explain`, `closing`. That is ten slides after the metadata block.

- [ ] **Step 6: Render**

```bash
python3 ~/.claude/skills/polk-slides/scripts/slidekit.py decks/pennchat-tutorial/pennchat-tutorial.md
```

Expected: no error, and no complaint about a missing image path.

- [ ] **Step 7: Commit**

```bash
git add decks/pennchat-tutorial
git commit -m "Add the four demo cue slides to the PennChat deck"
```

---

### Task 4: Visual QA

**Files:**
- Modify: `decks/pennchat-tutorial/pennchat-tutorial.md` (fixes only)

`polk-slides` treats this as a hard gate. The bar is legible text filling the slide with clear hierarchy — not merely "nothing clips."

- [ ] **Step 1: Run the integrity gate**

```bash
python3 ~/.claude/skills/polk-slides/scripts/review-deck.py decks/pennchat-tutorial/pennchat-tutorial.html
```

Expected: pass. A failure names the problem — fix it in the md and re-render.

- [ ] **Step 2: Render every slide to PNG**

```bash
~/.claude/skills/polk-slides/scripts/visual-check.sh decks/pennchat-tutorial/pennchat-tutorial.html
```

- [ ] **Step 3: Score every slide against the rubric**

Read `~/.claude/skills/polk-slides/references/visual-design-rubric.md`. Dispatch parallel subagents — one per slide, in a single message — each scoring its PNG against every criterion and returning a compact verdict. Give the subagents the PNG paths, never the multi-MB HTML.

Watch particularly for: the five-bullet "What PennChat Is" slide running long, and the four-column comparison table crowding at 16:9.

- [ ] **Step 4: Fix every failure in the md and re-render**

Edit `pennchat-tutorial.md`, never the HTML. Re-run Steps 1–3 on changed slides until every slide passes every criterion.

Overflow fix of choice: split the slide. Never shrink text to fit.

- [ ] **Step 5: Commit**

```bash
git add decks/pennchat-tutorial
git commit -m "Pass visual QA on the PennChat deck"
```

---

### Task 5: Content and voice review

**Files:**
- Modify: `decks/pennchat-tutorial/pennchat-tutorial.md` (fixes only)

- [ ] **Step 1: Dispatch both reviewers concurrently**

In a single message, spawn:
- `factual-reviewer` — pointed at `decks/pennchat-tutorial/pennchat-tutorial.md`, asked to extract every factual claim about PennChat, model names, data classifications, and cost.
- `voice-style-checker` — pointed at the same md, checking against Polk's voice standards and AI-tell lists.

They are independent. Do not serialize them.

- [ ] **Step 2: Verify the extracted claims**

Spawn `fact-verifier` with the claim list from Step 1, plus these authoritative sources:
- `https://isc.upenn.edu/resources/pennchat-pilot-FAQs`
- `index.html` lines 1617–1661 and 1848 in this repo (the portal's own vetted PennChat copy)

The portal copy has already been through review — where the deck and the portal disagree, the portal wins unless a Task 1 screenshot contradicts both.

- [ ] **Step 3: Fix everything flagged, then re-render and re-QA**

Apply fixes to the md. Re-run the render, then Task 4 Steps 1–3 on any slide whose text changed.

- [ ] **Step 4: Resolve any unverifiable claim**

Any claim the verifier could not confirm is cut from the slide or raised with Polk. Nothing ships unresolved.

- [ ] **Step 5: Commit**

```bash
git add decks/pennchat-tutorial
git commit -m "Apply factual and voice review to the PennChat deck"
```

---

### Task 6: Publish to the portal

**Files:**
- Create: `pennchat-tutorial.html` (copy of the rendered deck)
- Modify: `_config.yml`
- Modify: `ai-office-hours.html`

- [ ] **Step 1: Copy the rendered deck to the repo root**

```bash
cp decks/pennchat-tutorial/pennchat-tutorial.html pennchat-tutorial.html
```

The root copy is what GitHub Pages serves. The `decks/` copy stays as the build output next to its source.

- [ ] **Step 2: Keep the deck source off the published site**

In `_config.yml`, add `decks` under `exclude:`, below the existing `docs/superpowers` line:

```yaml
exclude:
  - CLAUDE.md
  - README.md
  - _config.yml
  - docs/superpowers
  - decks
```

This keeps the md and the raw screenshots out of the published tree, per the repo's no-dev-scaffolding convention. The root HTML copy is unaffected.

- [ ] **Step 3: Link the deck from the office hours page**

In `ai-office-hours.html`, find the `<ul class="resource-list">` in the Faculty Resources column (near line 462). Add this `<li>` as the first child, above the existing "AI at Penn Law" entry:

```html
        <li>
          <a href="pennchat-tutorial.html">
            <div>
              <span>PennChat: Overview &amp; Tutorial</span>
              <span class="res-desc">Slides from Session #1</span>
              <span class="res-url">ai-resources.ai-teaching-lab.org/pennchat-tutorial</span>
            </div>
            <span class="arrow">&rarr;</span>
          </a>
        </li>
```

Note the deliberate difference from its siblings: this is a same-site relative link, so it carries no `target="_blank" rel="noopener"`. Every class name matches the existing entries.

- [ ] **Step 4: Verify both pages locally**

```bash
open pennchat-tutorial.html ai-office-hours.html
```

Confirm: the deck opens on the title slide and arrow keys advance it; the new link on the office hours page is styled identically to its siblings and resolves.

- [ ] **Step 5: Commit and open the PR**

```bash
git add pennchat-tutorial.html _config.yml ai-office-hours.html
git commit -m "Publish the PennChat tutorial deck and link it from office hours"
git push -u origin pennchat-tutorial-deck
gh pr create --title "Add PennChat faculty tutorial deck" --body "$(cat <<'EOF'
Ten-slide deck for a 15-20 minute faculty Zoom session on PennChat, half
slides and half live demo, built to double as a short recorded video.

Four demo cue slides carry real captured PennChat screenshots, so the deck
reads coherently with no live demo at all.

Deck source is `decks/pennchat-tutorial/pennchat-tutorial.md`, excluded from
the published site along with its raw screenshots. Design spec:
`docs/superpowers/specs/2026-07-19-pennchat-tutorial-deck-design.md`.

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

- [ ] **Step 6: Rehearse the clock**

Present it start to finish against a timer, demos included. If it runs past 20 minutes, the cut is Demo 4's live build — take the flex path already printed on that slide. Report the actual timing to Polk.

---

## Success Criteria

1. Deck runs start to finish in 15–20 minutes with the demos.
2. Every UI claim traces to a screenshot captured in Task 1.
3. Deck reads coherently with zero live demo — the video path works.
4. Every slide passes every criterion in the visual design rubric.
5. Voice is Polk's; no AI tells; faculty-only framing.
6. `decks/` and `docs/superpowers/` are absent from the published site.

---

## Outcome (2026-07-20)

Shipped as PR #9. The portal corrections it surfaced went out separately as
PR #10. What differs from the plan above, and why:

### Structure

- **Eleven slides, not ten.** A use-cases slide was added to carry the portal's
  "Everyday AI" framing; it later became "Pros and Cons of PennChat" when Polk
  rewrote it.
- **Three demos, not four.** The standalone document-upload demo was folded in:
  the TA agent answers from its attached patents, so it carries that beat. Demo 1
  then became a document upload in its own right (see below), so the deck still
  shows uploading — just once, not twice.
- **Demo 1 changed entirely.** Originally "log in and do one real task" with a
  memo-tightening prompt. It became an adversarial read of Polk's exam-taker
  paper: attach the draft, ask it to read as a hostile referee and to say what
  would fix each problem.
- **Demo 1 is pre-run, never performed live.** A 28-page adversarial read did not
  return within 2.5 minutes of waiting. Performing that live would spend a third
  of the demo budget on a loading dot. Polk sends the prompt before the session
  and switches to the finished tab.

### Claims cut

- **"Set it as your default"** could not be verified and was replaced with
  *pin*. No such control exists in Settings; hovering a non-default model offers
  only a pin. The portal made the same claim and was corrected in PR #10.
- **"Free during the pilot"** would be stale for a Fall session. The table now
  reads "Free\*, credit-based" with a TBD footnote. Portal corrected likewise.
- **"Every custom-GPT capability has a direct equivalent"** was softened to
  *most*, because agents cannot be shared — which the deck itself says. Same
  overclaim existed on the portal; corrected in PR #10.
- **The SSN / card-data / PHI exclusions were removed from the deck** at Polk's
  direction. They remain on the portal in two places. Flagged twice, decided.

### Skill fix

`polk-slides`' title slide was clipping its logo lockup — "University of
Pennsylvania" sliced mid-word — on every deck, not just this one. Two children
of `.title-slide` carried `margin-top: auto` while the container was also
`justify-content: center`; with seven stacked elements the content exceeded the
16:9 frame and the competing auto margins pushed it past both edges. Fixed in
`~/.claude/skills/polk-slides/PENN_THEME.md` and mirrored to `claude-sync`.

The shield still meets the top edge. Additional padding does not move it, so it
is constrained elsewhere in `.slide-content` — left alone as a bleed rather than
a defect.

### Working notes for future edits

- Sub-bullets nest with a **two-space indent**. A `--` prefix is silently
  discarded — four sub-bullets vanished this way with no warning.
- **Em-dashes become commas** at render (house rule). Use colons in titles.
- slidekit **scales type to fill**, so removing a bullet or callout can cause
  overflow rather than fix it. Shorten lines before cutting them. A callout
  often acts as a size constraint.
- The root `pennchat-tutorial.html` is a **manual copy**, not a symlink.
  Re-copy it after every render or the published deck goes stale.

### Not done

- Timing rehearsal against a clock. The 15-20 minute estimate is unmeasured.
- Projects and MCP Settings remain undocumented on the portal — observed to
  exist, never exercised.
- The pilot's mid-August end date and credit model are unconfirmed with ISC.
