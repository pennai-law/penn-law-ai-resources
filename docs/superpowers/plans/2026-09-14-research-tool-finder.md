# Research Tool Finder + AI for Research — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the router's broken promise — "…work out which tool fits the task"
currently lands on a box that only picks a PennChat model — with a real task-based Tool
Finder spanning the whole roster, a companion "AI for Research" section organized by
research verb, and a new CourtListener card so public case-law search has somewhere to
point.

**Architecture:** Four surgical edits to a single file, `index.html`. New CSS in the
shared `<style>` block, new markup inside the Using AI tab-pane, new JS after the
existing `goToTab()` function, one new card in the Getting Started tab's Legal-Specific
Tools grid, and a small id rename so the router's existing link points at the new
content instead of the old one. No new files, no build step, no dependencies.

**Tech Stack:** Static HTML with inline CSS/JS. Served by GitHub Pages from `main`,
root `/`. No build, no test framework.

**Spec:** `docs/superpowers/specs/2026-09-14-research-tool-finder-design.md`

## Global Constraints

- **Only `index.html` is modified.** No new files, no assets, no CSS/JS files.
- **No `:has()` CSS selector.** This codebase has no precedent for it; toggle a
  `chip-active` class via JS instead, matching the existing `.tab-btn.active` pattern.
- **Deep links across tabs go through `goToCard(tab, cardId)`** (`index.html:2453`,
  signature unchanged) or `goToTab(target)` (`index.html:2463`). Never a bare
  `href="#id"` for an anchor that lives on a different tab-pane than the link.
- **Voice is Polk Wagner's.** Direct, active, collegial. No hedging, no "leverage,"
  "utilize," "ensure," "robust," "stakeholders," "moving forward," "deep dive."
- **`&mdash;`/`&rsquo;` for new prose**, matching the majority convention in this file's
  callout boxes. Do not introduce literal Unicode em-dashes or curly quotes; use the
  entities, as the surrounding callouts do.
- **Never state a fact not in this plan.** The risk-tier assignments in Task 1's
  `FINDER_TOOLS` object are the assumptions listed in the spec's "Assumptions to verify
  before publishing" section. Implement them as written; do not research or "correct"
  them mid-task.
- **The search index is live-DOM.** `performSearch` walks `h2, h3, p, li` inside
  `.tab-pane` at query time. Put new copy in those tags, never bare `<div>` text, if it
  should be searchable.
- **Any `id` inside a pane is deep-linkable** — `activateFromHash` resolves the owning
  tab via `closest('.tab-pane')`. No routing table to update.

---

### Task 1: Add the Tool Finder to the Using AI tab

**Files:**
- Modify: `index.html:841` (CSS, insert after) — new `<style>` rules
- Modify: `index.html:1908` (HTML, insert after the closing `</div>` of `pennchat-no-web`, before `<div id="pennchat-models"`)
- Modify: `index.html:2468` (JS, insert after `goToTab()`'s closing brace, before `tabs.forEach(...)`)

**Interfaces:**
- Consumes: `goToCard(tab, cardId)` and `activateTab(tab)`, already defined.
- Produces: `#tool-finder` (temporary id, re-anchored to `#which-model` in Task 4);
  `FINDER_TOOLS` (JS object) and `renderFinder()` (JS function) — Task 3 extends
  `FINDER_TOOLS.research` with the CourtListener entry.

- [ ] **Step 1: Confirm the file is in the expected state**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
grep -c 'id="which-model"' index.html
grep -c "STYLED TIPS LIST" index.html
```
Expected: `1` and `1`. If either prints `0`, stop — the file has moved since this plan
was written.

- [ ] **Step 2: Add the Tool Finder and AI-for-Research CSS**

Find this exact fragment at `index.html:836-844`:

```css
    content: '\2713';
    position: absolute;
    left: 0;
    color: var(--gold);
    font-weight: 600;
  }

  /* ── STYLED TIPS LIST ── */
  .tips-list {
```

Replace with (this inserts the new block between the closing `}` and the "STYLED TIPS
LIST" comment, changing nothing else):

```css
    content: '\2713';
    position: absolute;
    left: 0;
    color: var(--gold);
    font-weight: 600;
  }

  /* ── TOOL FINDER ── */
  .finder {
    background: #fff; border: 1px solid var(--border); border-left: 4px solid var(--navy);
    border-radius: 0 10px 10px 0; padding: 1.4rem 1.75rem 1.6rem; margin-bottom: 1.4rem;
  }
  .finder-sub { font-size: 14px; color: var(--dark-gray); max-width: 640px; margin: 0 0 1.2rem; }
  .finder-controls { display: grid; grid-template-columns: 1fr 1fr; gap: 1.3rem 2rem; margin-bottom: 1.3rem; }
  @media (max-width: 720px) { .finder-controls { grid-template-columns: 1fr; } }
  .finder fieldset { border: 0; padding: 0; margin: 0; }
  .finder legend { font-size: 11px; font-weight: 700; letter-spacing: 0.07em; text-transform: uppercase; color: var(--navy); margin-bottom: 0.55rem; padding: 0; border: 0; }
  .chip-row { display: flex; flex-wrap: wrap; gap: 0.5rem; }
  .chip {
    position: relative; font-family: 'DM Sans', sans-serif; font-size: 13.5px;
    background: var(--warm-gray); border: 1px solid var(--border); color: var(--dark-gray);
    border-radius: 999px; padding: 0.4rem 0.85rem; cursor: pointer;
    transition: background .15s, border-color .15s, color .15s;
  }
  .chip input { position: absolute; inset: 0; opacity: 0; cursor: pointer; margin: 0; }
  .chip input:focus-visible { outline: 2px solid var(--red); outline-offset: 2px; }
  .chip.chip-active { background: var(--navy); border-color: var(--navy); color: #fff; font-weight: 500; }
  .finder-results-head { display: flex; align-items: baseline; justify-content: space-between; gap: 1rem; margin: 0 0 0.7rem; padding-top: 1.2rem; border-top: 1px solid var(--warm-gray); }
  .finder-results-head h4 { font-family: 'EB Garamond', serif; font-size: 1.05rem; color: var(--navy); font-weight: 500; margin: 0; }
  .finder-count { font-size: 12.5px; color: var(--mid-gray); font-variant-numeric: tabular-nums; }
  .finder-results { display: flex; flex-direction: column; gap: 0.65rem; }
  .finder-result {
    display: grid; grid-template-columns: auto 1fr auto; gap: 0 1.1rem; align-items: start;
    background: var(--warm-gray); border: 1px solid var(--border); border-radius: 8px; padding: 0.85rem 1.1rem;
  }
  .finder-rank { font-family: 'EB Garamond', serif; font-size: 1.4rem; color: var(--gold); font-weight: 500; width: 1.3rem; text-align: center; line-height: 1.3; }
  .finder-name { font-weight: 600; color: var(--navy); font-size: 14.5px; }
  .finder-why { font-size: 13px; color: var(--dark-gray); margin-top: 0.15rem; }
  .finder-go { align-self: center; font-size: 12.5px; font-weight: 600; color: var(--navy); text-decoration: none; white-space: nowrap; border: 1px solid var(--border); padding: 0.3rem 0.65rem; border-radius: 6px; }
  .finder-go:hover { background: #fff; }
  .finder-empty { font-size: 13.5px; color: var(--mid-gray); font-style: italic; padding: 0.4rem 0; margin: 0; }

  /* ── AI FOR RESEARCH ── */
  .verb-row { display: grid; grid-template-columns: 180px 1fr; gap: 0 1.6rem; padding: 1.05rem 0; border-top: 1px solid var(--warm-gray); }
  .verb-row:first-of-type { border-top: 1px solid var(--border); }
  @media (max-width: 640px) { .verb-row { grid-template-columns: 1fr; gap: 0.35rem 0; } }
  .verb-label { font-family: 'EB Garamond', serif; font-size: 1.1rem; color: var(--navy); font-weight: 500; }
  .verb-label small { display: block; font-family: 'DM Sans', sans-serif; font-size: 11px; color: var(--mid-gray); font-weight: 500; text-transform: uppercase; letter-spacing: 0.05em; margin-top: 0.2rem; }
  .verb-detail p { margin: 0 0 0.5rem; font-size: 13.5px; }
  .verb-detail p:last-child { margin-bottom: 0; }
  .verb-tools { font-size: 12.5px; color: var(--mid-gray); }
  .verb-tools a { color: var(--navy); font-weight: 600; text-decoration: none; border-bottom: 1px dotted var(--navy); }

  /* ── STYLED TIPS LIST ── */
  .tips-list {
```

- [ ] **Step 3: Verify the CSS landed once, cleanly**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
grep -c "TOOL FINDER ── \*/" index.html
grep -c "AI FOR RESEARCH ── \*/" index.html
python3 -c "
import html.parser
class P(html.parser.HTMLParser):
    def error(self,m): raise SystemExit('PARSE ERROR: '+m)
P().feed(open('index.html',encoding='utf-8').read()); print('parses OK')
"
```
Expected: `1`, `1`, `parses OK`.

- [ ] **Step 4: Insert the Tool Finder markup**

Find this exact fragment at `index.html:1907-1909`:

```html
      </ul>
    </div>

    <div id="pennchat-models" style="border-left:4px solid var(--navy); background:var(--cream); border-radius:0 10px 10px 0; padding:1.4rem 1.75rem; margin-bottom:1.4rem; scroll-margin-top:70px;">
```

Replace with:

```html
      </ul>
    </div>

    <div class="finder" id="tool-finder">
      <div style="display:flex; align-items:center; gap:0.6rem; flex-wrap:wrap; margin-bottom:0.4rem;">
        <span style="font-size:10.5px; font-weight:700; letter-spacing:0.09em; text-transform:uppercase; color:#fff; background:var(--navy); padding:0.22rem 0.65rem; border-radius:999px;">Using AI</span>
        <h3 style="margin:0; font-family:'EB Garamond',serif; font-size:1.3rem; color:var(--navy); font-weight:500;">Find the right tool for the task</h3>
      </div>
      <p class="finder-sub">Across PennChat, Claude.ai, the legal-specific tools, and the agentic tools &mdash; not just which PennChat model to pick (that&rsquo;s below).</p>

      <div class="finder-controls">
        <fieldset>
          <legend>What are you doing?</legend>
          <div class="chip-row" id="finder-task-chips">
            <label class="chip"><input type="radio" name="finder-task" value="draft"> Draft or summarize</label>
            <label class="chip chip-active"><input type="radio" name="finder-task" value="research" checked> Case-law / doctrinal research</label>
            <label class="chip"><input type="radio" name="finder-task" value="citecheck"> Cite-check or verify sources</label>
            <label class="chip"><input type="radio" name="finder-task" value="review"> Long document / contract review</label>
            <label class="chip"><input type="radio" name="finder-task" value="automate"> Automate a repeatable task</label>
          </div>
        </fieldset>
        <fieldset>
          <legend>How sensitive is the material?</legend>
          <div class="chip-row" id="finder-risk-chips">
            <label class="chip"><input type="radio" name="finder-risk" value="low"> Low</label>
            <label class="chip chip-active"><input type="radio" name="finder-risk" value="moderate" checked> Moderate</label>
            <label class="chip"><input type="radio" name="finder-risk" value="high"> High</label>
          </div>
        </fieldset>
      </div>

      <div class="finder-results-head">
        <h4>Recommended</h4>
        <span class="finder-count" id="finder-count"></span>
      </div>
      <div class="finder-results" id="finder-results"></div>
    </div>

    <div id="pennchat-models" style="border-left:4px solid var(--navy); background:var(--cream); border-radius:0 10px 10px 0; padding:1.4rem 1.75rem; margin-bottom:1.4rem; scroll-margin-top:70px;">
```

- [ ] **Step 5: Add the Tool Finder JS**

Find this exact fragment at `index.html:2462-2470`:

```js
  // Switch tabs from a link and record it in the URL, so a copied address
  // opens on this tab and the back button returns to the previous one.
  function goToTab(target) {
    activateTab(target);
    history.pushState(null, '', '#' + target);
    window.scrollTo({ top: document.querySelector('.tab-nav-wrap').offsetTop - 56, behavior: 'smooth' });
    return false;
  }

  tabs.forEach(btn => {
```

Replace with:

```js
  // Switch tabs from a link and record it in the URL, so a copied address
  // opens on this tab and the back button returns to the previous one.
  function goToTab(target) {
    activateTab(target);
    history.pushState(null, '', '#' + target);
    window.scrollTo({ top: document.querySelector('.tab-nav-wrap').offsetTop - 56, behavior: 'smooth' });
    return false;
  }

  // ── TOOL FINDER (Using AI tab) ──
  // Risk-tier assignments are read off each tool's existing card-badge; see
  // docs/superpowers/specs/2026-09-14-research-tool-finder-design.md, "Assumptions
  // to verify before publishing," for which ones are interpretive rather than a
  // direct badge read.
  const FINDER_TOOLS = {
    draft: [
      { name: 'PennChat', why: 'Free with your PennKey, no setup &mdash; the safe default for everyday drafting.', risk: ['low','moderate','high'], go: () => goToCard('getting-started','card-pennchat') },
      { name: 'Claude.ai', why: 'Same models, direct access &mdash; better for iterating on a longer draft across a session.', risk: ['low','moderate'], go: () => goToCard('getting-started','card-claude') },
    ],
    research: [
      { name: 'Westlaw AI-Assisted Research', why: 'Natural-language search and conversational authority retrieval, under your existing subscription.', risk: ['low','moderate','high'], go: () => goToCard('getting-started','card-westlaw') },
      { name: 'Lexis+ AI', why: 'Conversational search with direct case verification built in.', risk: ['low','moderate','high'], go: () => goToCard('getting-started','card-lexis') },
      { name: 'Harvey', why: 'Penn Law&rsquo;s enterprise legal-research tool; your data isn&rsquo;t used for training.', risk: ['low','moderate'], go: () => goToCard('getting-started','card-harvey') },
    ],
    citecheck: [
      { name: 'Lexis+ AI', why: 'Direct case verification is a named feature, not a side effect of chat.', risk: ['low','moderate','high'], go: () => goToCard('getting-started','card-lexis') },
      { name: 'Harvey', why: 'Cite-checking works inside a full document, not just a pasted excerpt.', risk: ['low','moderate'], go: () => goToCard('getting-started','card-harvey') },
    ],
    review: [
      { name: 'Harvey', why: 'Built for exactly this &mdash; long-document legal review under a Penn enterprise agreement.', risk: ['low','moderate'], go: () => goToCard('getting-started','card-harvey') },
      { name: 'Legora', why: 'Comparable document-analysis workflow; six-month retention on inactive projects.', risk: ['low','moderate'], go: () => goToCard('getting-started','card-legora') },
      { name: 'Claude.ai', why: 'Bring your own document for a single close read outside a legal-specific tool.', risk: ['low','moderate'], go: () => goToCard('getting-started','card-claude') },
    ],
    automate: [
      { name: 'Claude Code', why: 'Multi-step and agentic &mdash; hands off a task across files rather than one chat turn at a time.', risk: ['low','moderate'], go: () => goToCard('agentic-ai','card-ag-claude-code') },
      { name: 'The Penn LLM Gateway', why: 'Programmatic API access when the task is really a script, not a conversation.', risk: ['low','moderate'], go: () => goToCard('agentic-ai','ai-skills') },
    ],
  };

  function renderFinder() {
    const taskEl = document.querySelector('input[name="finder-task"]:checked');
    const riskEl = document.querySelector('input[name="finder-risk"]:checked');
    if (!taskEl || !riskEl) return;
    const rows = (FINDER_TOOLS[taskEl.value] || []).filter(t => t.risk.includes(riskEl.value));
    const results = document.getElementById('finder-results');
    const count = document.getElementById('finder-count');
    count.textContent = rows.length ? rows.length + (rows.length === 1 ? ' match' : ' matches') : '';
    if (!rows.length) {
      results.innerHTML = '<p class="finder-empty">Nothing cleared for that combination yet &mdash; check the full access table above.</p>';
      return;
    }
    results.innerHTML = rows.map((t, i) => (
      '<div class="finder-result"><div class="finder-rank">' + (i + 1) + '</div><div>' +
      '<div class="finder-name">' + t.name + '</div>' +
      '<div class="finder-why">' + t.why + '</div></div>' +
      '<a class="finder-go" href="#" data-idx="' + i + '">See tool &rsaquo;</a></div>'
    )).join('');
    results.querySelectorAll('.finder-go').forEach((a, i) => {
      a.addEventListener('click', (e) => { e.preventDefault(); rows[i].go(); });
    });
  }

  document.querySelectorAll('input[name="finder-task"], input[name="finder-risk"]').forEach(el => {
    el.addEventListener('change', () => {
      el.closest('.chip-row').querySelectorAll('.chip').forEach(c => c.classList.remove('chip-active'));
      el.closest('.chip').classList.add('chip-active');
      renderFinder();
    });
  });
  renderFinder();

  tabs.forEach(btn => {
```

- [ ] **Step 6: Verify the JS is well-formed**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
node -e "
const fs = require('fs');
const s = fs.readFileSync('index.html', 'utf8');
const m = s.match(/<script>([\s\S]*)<\/script>\s*<\/body>/);
if (!m) { console.error('could not isolate main script block'); process.exit(1); }
new Function(m[1]);
console.log('JS syntax OK');
"
```
Expected: `JS syntax OK`. (If `node` isn't installed, skip this step and rely on Step 7's
browser check — the site has no build step and doesn't otherwise require Node.)

- [ ] **Step 7: Open the page and exercise the Tool Finder**

Run: `open index.html`, click the **Using AI** tab.

Check by eye: the Tool Finder box renders above "Which model should I use?", with
"Case-law / doctrinal research" and "Moderate" pre-selected (navy chips), showing 3
results (Westlaw, Lexis+ AI, Harvey).

Click **Draft or summarize**. Expected: 2 results (PennChat, Claude.ai), the clicked
chip turns navy, the previous one returns to its default gray.

Click **High** under sensitivity with **Draft or summarize** still selected. Expected:
1 result (PennChat only — Claude.ai's Enterprise approval doesn't cover High Risk).

Click "See tool ›" on PennChat. Expected: switches to Getting Started and scrolls the
PennChat card into view.

- [ ] **Step 8: Commit**

```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
git add index.html
git commit -m "content: add a task-based Tool Finder to the Using AI tab

Replaces nothing — sits above the existing PennChat-model box, which
still answers a real, narrower question. Recommends across the whole
roster (PennChat, Claude.ai, Harvey, Legora, Westlaw, Lexis+, Claude
Code, LLM Gateway) by task and Penn data-risk tier, deep-linking to
each tool's existing card rather than re-describing it.

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>"
```

---

### Task 2: Add the AI for Research section

**Files:**
- Modify: `index.html` — insert immediately after the Tool Finder's closing `</div>`, before `<div id="pennchat-models"`

**Interfaces:**
- Consumes: `goToCard(tab, cardId)`.
- Produces: nothing consumed downstream in this task. Task 3 edits this section's
  "Find it" row once the CourtListener card exists.

- [ ] **Step 1: Insert the section**

Find this exact fragment (the end of Task 1's inserted block):

```html
      <div class="finder-results" id="finder-results"></div>
    </div>

    <div id="pennchat-models"
```

Replace with:

```html
      <div class="finder-results" id="finder-results"></div>
    </div>

    <div style="margin-bottom: 2rem;">
      <h3 style="font-family:'EB Garamond',serif; font-size:1.3rem; color:var(--navy); font-weight:500; margin-bottom:0.3rem;">AI for Research</h3>
      <p style="font-size:13.5px; color:var(--mid-gray); max-width:640px; margin:0 0 1.4rem;">Same tools as above, organized by the shape of a research task rather than by vendor &mdash; for the work that doesn&rsquo;t fit neatly into &ldquo;draft&rdquo; or &ldquo;review.&rdquo;</p>

      <div class="verb-row">
        <div class="verb-label">Find it<small>Case law &amp; precedent</small></div>
        <div class="verb-detail">
          <p>Natural-language search across cases, statutes, and dockets, with authority retrieval built for legal citation rather than general web search.</p>
          <p class="verb-tools">Start with: <a href="#" onclick="return goToCard('getting-started','card-westlaw');">Westlaw AI-Assisted Research</a>, <a href="#" onclick="return goToCard('getting-started','card-lexis');">Lexis+ AI</a> &mdash; for public dockets and opinions specifically, CourtListener&rsquo;s API is worth adding to the roster below.</p>
        </div>
      </div>
      <div class="verb-row">
        <div class="verb-label">Verify it<small>Citations &amp; sourcing</small></div>
        <div class="verb-detail">
          <p>Confirm a citation is real, still good law, and says what a draft claims it says &mdash; the step every AI-assisted draft needs before it leaves your desk.</p>
          <p class="verb-tools">Start with: <a href="#" onclick="return goToCard('getting-started','card-lexis');">Lexis+ AI</a>&rsquo;s direct case verification, or <a href="#" onclick="return goToCard('getting-started','card-harvey');">Harvey</a> for citation checking inside a longer document.</p>
        </div>
      </div>
      <div class="verb-row">
        <div class="verb-label">Synthesize it<small>Literature &amp; sources</small></div>
        <div class="verb-detail">
          <p>Pull a body of scholarship or case law into a coherent research memo &mdash; the task &ldquo;chat, draft, summarize&rdquo; undersells once the source pile gets past a dozen documents.</p>
          <p class="verb-tools">Start with: <a href="#" onclick="return goToCard('getting-started','card-claude');">Claude.ai</a> (bring your own documents) or <a href="#" onclick="return goToCard('getting-started','card-legora');">Legora</a> for a legal-specific workflow.</p>
        </div>
      </div>
      <div class="verb-row">
        <div class="verb-label">Analyze it<small>Empirical &amp; data-driven</small></div>
        <div class="verb-detail">
          <p>Docket-level or patent-data pulls, coding a dataset, running the numbers behind an empirical claim &mdash; closer to a data-analysis workflow than a chatbot conversation.</p>
          <p class="verb-tools">Start with: <a href="#" onclick="return goToCard('agentic-ai','card-ag-claude-code');">Claude Code</a> or the <a href="#" onclick="return goToCard('agentic-ai','ai-skills');">Penn LLM Gateway</a> for programmatic access; not a fit for PennChat&rsquo;s chat interface alone.</p>
        </div>
      </div>
    </div>

    <div id="pennchat-models"
```

- [ ] **Step 2: Verify the section renders and the anchors resolve**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
grep -c "AI for Research</h3>" index.html
grep -c 'class="verb-row"' index.html
```
Expected: `1` and `4`.

- [ ] **Step 3: Confirm it's inside the `use-cases` (Using AI) pane**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
python3 - <<'PY'
s = open('index.html', encoding='utf-8').read()
uc = s.index('id="use-cases"')
nxt = s.index('class="tab-pane"', uc)
finder = s.index('AI for Research</h3>')
print("inside use-cases pane:", uc < finder < nxt)
PY
```
Expected: `inside use-cases pane: True`

- [ ] **Step 4: Click every link in the browser**

Run: `open index.html`, click the **Using AI** tab, scroll to "AI for Research."

Click each of the four rows' links in turn (Westlaw, Lexis+ AI ×2, Harvey, Claude.ai,
Legora, Claude Code, Penn LLM Gateway). Expected: every one switches to the correct tab
(Getting Started for the first six, Agentic & Advanced for the last two) and scrolls
its target into view. The "CourtListener's API is worth adding" sentence in the "Find
it" row is a plain caveat, not a link — that's expected until Task 3.

- [ ] **Step 5: Commit**

```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
git add index.html
git commit -m "content: add an AI for Research section to the Using AI tab

Four rows (Find it / Verify it / Synthesize it / Analyze it) reframing
the existing tool catalog around the shape of a research task, for
work that doesn't fit neatly into 'draft' or 'review.' No new tool
descriptions, only pointers into the existing catalog.

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>"
```

---

### Task 3: Add a CourtListener card and wire it up

**Files:**
- Modify: `index.html:1381` (Legal-Specific Tools intro sentence)
- Modify: `index.html` — Legal-Specific Tools `.card-grid`, after the Lexis+ AI card
- Modify: Task 1's `FINDER_TOOLS.research` array
- Modify: Task 2's "Find it" row

**Interfaces:**
- Consumes: `FINDER_TOOLS` (Task 1), the "Find it" verb-row (Task 2).
- Produces: `id="card-courtlistener"`, referenced by both.

- [ ] **Step 1: Widen the Legal-Specific Tools intro sentence**

Find this exact line:

```html
    <p style="font-size:14px; color:var(--mid-gray); margin-bottom:1.25rem; max-width:640px; line-height:1.65;">Built specifically for legal work &mdash; trained on legal data and designed for legal research and drafting.</p>
```

Replace with:

```html
    <p style="font-size:14px; color:var(--mid-gray); margin-bottom:1.25rem; max-width:640px; line-height:1.65;">Built specifically for legal work &mdash; AI-powered drafting and analysis tools, plus core legal research infrastructure like case law and docket databases.</p>
```

- [ ] **Step 2: Add the CourtListener card**

Find this exact fragment (the end of the Lexis+ AI card and the grid's closing tag):

```html
        <div class="card-links">
          <a class="card-link" href="https://plus.lexis.com" target="_blank" rel="noopener">Open Lexis+</a>
        </div>
      </div>

    </div>

    <h3 id="sec-productivity-tools"
```

Replace with:

```html
        <div class="card-links">
          <a class="card-link" href="https://plus.lexis.com" target="_blank" rel="noopener">Open Lexis+</a>
        </div>
      </div>

      <div class="card">
        <div class="card-title-wrap">
          <div class="card-icon" style="background:var(--navy); color:#fff;">CL</div>
          <h3 id="card-courtlistener">CourtListener</h3>
        </div>
        <div class="card-badges">
          <span class="card-badge" style="background:var(--warm-gray); color:var(--dark-gray); border:1px solid var(--border);">Free &amp; Public</span>
        </div>
        <p>Free legal research from the Free Law Project, a 501(c)(3) nonprofit &mdash; case law, judges, oral argument audio, and RECAP (crowdsourced federal court dockets). No Penn license or login needed to search; a free account raises API rate limits for programmatic use.</p>
        <div class="card-spec"><strong>Not Penn-provided:</strong> nothing to request through ITS &mdash; and no Penn data-risk review applies, since it&rsquo;s a public-records search tool rather than a place you send Penn material.</div>
        <div class="card-links">
          <a class="card-link" href="https://www.courtlistener.com" target="_blank" rel="noopener">Open CourtListener</a>
          <a class="card-link" href="https://www.courtlistener.com/help/api/rest/" target="_blank" rel="noopener">REST API docs</a>
        </div>
      </div>

    </div>

    <h3 id="sec-productivity-tools"
```

- [ ] **Step 3: Add CourtListener to the Tool Finder's research results**

Find this exact line inside `FINDER_TOOLS.research` (added in Task 1, Step 5):

```js
      { name: 'Harvey', why: 'Penn Law&rsquo;s enterprise legal-research tool; your data isn&rsquo;t used for training.', risk: ['low','moderate'], go: () => goToCard('getting-started','card-harvey') },
    ],
    citecheck: [
```

Replace with:

```js
      { name: 'Harvey', why: 'Penn Law&rsquo;s enterprise legal-research tool; your data isn&rsquo;t used for training.', risk: ['low','moderate'], go: () => goToCard('getting-started','card-harvey') },
      { name: 'CourtListener', why: 'Free public case law, dockets, and oral arguments &mdash; not a Penn-licensed tool, so nothing to request.', risk: ['low','moderate','high'], go: () => goToCard('getting-started','card-courtlistener') },
    ],
    citecheck: [
```

- [ ] **Step 4: Update the "Find it" verb-row's caveat into a real link**

Find this exact line (from Task 2):

```html
          <p class="verb-tools">Start with: <a href="#" onclick="return goToCard('getting-started','card-westlaw');">Westlaw AI-Assisted Research</a>, <a href="#" onclick="return goToCard('getting-started','card-lexis');">Lexis+ AI</a> &mdash; for public dockets and opinions specifically, CourtListener&rsquo;s API is worth adding to the roster below.</p>
```

Replace with:

```html
          <p class="verb-tools">Start with: <a href="#" onclick="return goToCard('getting-started','card-westlaw');">Westlaw AI-Assisted Research</a>, <a href="#" onclick="return goToCard('getting-started','card-lexis');">Lexis+ AI</a> &mdash; or <a href="#" onclick="return goToCard('getting-started','card-courtlistener');">CourtListener</a> for public dockets and opinions specifically, free and with no Penn license needed.</p>
```

- [ ] **Step 5: Verify all four edits landed**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
grep -c 'AI-powered drafting and analysis tools, plus core legal research infrastructure' index.html
grep -c 'id="card-courtlistener"' index.html
grep -c "name: 'CourtListener'" index.html
grep -c "for public dockets and opinions specifically, free and with no Penn license needed" index.html
```
Expected: `1` four times. `card-courtlistener` should appear exactly twice total (the
card's own `id` and the two links to it) — check separately:

```bash
grep -c "card-courtlistener" index.html
```
Expected: `3` (the id definition, the Tool Finder's `go` reference, and the AI for
Research link).

- [ ] **Step 6: Confirm the HTML still parses and the JS is still well-formed**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
python3 -c "
import html.parser
class P(html.parser.HTMLParser):
    def error(self,m): raise SystemExit('PARSE ERROR: '+m)
P().feed(open('index.html',encoding='utf-8').read()); print('parses OK')
"
node -e "
const fs = require('fs');
const s = fs.readFileSync('index.html', 'utf8');
const m = s.match(/<script>([\s\S]*)<\/script>\s*<\/body>/);
new Function(m[1]);
console.log('JS syntax OK');
" 2>/dev/null || echo "node not available, skip — verify in browser instead"
```
Expected: `parses OK` and either `JS syntax OK` or the skip message.

- [ ] **Step 7: Exercise the wiring in the browser**

Run: `open index.html`, click **Getting Started**, scroll to Legal-Specific Tools.

Check by eye: five cards now (Harvey, Legora, Westlaw, Lexis+ AI, CourtListener), the
new card has a navy "CL" icon badge and a gray "Free & Public" pill, and the grid still
lays out two-per-row without breaking (CourtListener sits alone on the last row).

Switch to **Using AI**. In the Tool Finder, select "Case-law / doctrinal research" +
"High." Expected: now **2** results (Westlaw/Lexis+ still filtered by the "Legal
Research Terms" assumption, plus CourtListener). Click CourtListener's "See tool ›".
Expected: jumps to Getting Started and scrolls the new card into view.

Scroll to "AI for Research" → "Find it." Click "CourtListener." Expected: same
destination.

- [ ] **Step 8: Commit**

```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
git add index.html
git commit -m "content: add a CourtListener card and wire it into the Tool Finder

Free, public legal research (Free Law Project) had no card and
nothing for the Tool Finder's public-case-law result to point at. No
Penn data-risk badge assigned — it's a public-records search tool,
not a place Penn material gets sent, and carries no Penn licensing
review to report.

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>"
```

---

### Task 4: Re-anchor `#which-model` to the Tool Finder

**Files:**
- Modify: `index.html:513` (section-heading `scroll-margin-top` selector list)
- Modify: `index.html:1113` (router link label)
- Modify: `index.html:1833` (tab-jump-link label)
- Modify: Task 1's Tool Finder `<h3>` (add `id="which-model"`)
- Modify: the old PennChat-model `<h3>` (rename `id="which-model"` → `id="which-pennchat-model"`)

**Interfaces:**
- Consumes: the Tool Finder markup from Task 1.
- Produces: nothing consumed downstream — this is the final task before verification.

- [ ] **Step 1: Add `#which-pennchat-model` to the scroll-margin selector list**

Find this exact line:

```css
  .section-title, [id^="sec-"], #orientation, #tools-at-penn, #adjunct-faculty, #data-risk, #better-results, #which-model, #claude-models, #pennchat-models, #pennchat-agents, #ai-skills {
```

Replace with:

```css
  .section-title, [id^="sec-"], #orientation, #tools-at-penn, #adjunct-faculty, #data-risk, #better-results, #which-model, #which-pennchat-model, #claude-models, #pennchat-models, #pennchat-agents, #ai-skills {
```

(`#which-model` stays in the list unchanged — it will now apply to the Tool Finder's
heading instead of the old one, which is exactly what Step 4 below relies on.)

- [ ] **Step 2: Rename the old heading's id**

Find this exact line (inside the `pennchat-models` box, now the *second* box in the
tab, after the Tool Finder):

```html
        <h3 id="which-model" style="margin:0; scroll-margin-top:70px; font-family:'EB Garamond',serif; font-size:1.3rem; color:var(--navy); font-weight:500;">Which model should I use?</h3>
```

Replace with:

```html
        <h3 id="which-pennchat-model" style="margin:0; scroll-margin-top:70px; font-family:'EB Garamond',serif; font-size:1.3rem; color:var(--navy); font-weight:500;">Which model should I use?</h3>
```

- [ ] **Step 3: Give the Tool Finder heading the freed-up id**

Find this exact line (Task 1's Tool Finder header):

```html
        <h3 style="margin:0; font-family:'EB Garamond',serif; font-size:1.3rem; color:var(--navy); font-weight:500;">Find the right tool for the task</h3>
```

Replace with:

```html
        <h3 id="which-model" style="margin:0; scroll-margin-top:70px; font-family:'EB Garamond',serif; font-size:1.3rem; color:var(--navy); font-weight:500;">Find the right tool for the task</h3>
```

- [ ] **Step 4: Update the router link's visible label**

Find this exact line:

```html
      <li><a href="#which-model">&hellip;work out which tool fits the task.<span class="where">Using AI &middot; Which model should I use?</span></a></li>
```

Replace with:

```html
      <li><a href="#which-model">&hellip;work out which tool fits the task.<span class="where">Using AI &middot; Find the right tool for the task</span></a></li>
```

(The `href` is unchanged — it already pointed at `#which-model`, which now resolves to
the Tool Finder instead of the old box.)

- [ ] **Step 5: Update the tab-jump-link label**

Find this exact line:

```html
      <a class="tab-jump-link featured" href="#which-model">Model Picker &darr;</a>
```

Replace with:

```html
      <a class="tab-jump-link featured" href="#which-model">Tool Finder &darr;</a>
```

- [ ] **Step 6: Verify exactly one element owns each id**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
grep -c 'id="which-model"' index.html
grep -c 'id="which-pennchat-model"' index.html
```
Expected: `1` and `1`. If `id="which-model"` prints `2`, Step 2 or Step 3 targeted the
wrong occurrence — the file now has a duplicate id, which is a real bug (the browser
will resolve `#which-model` to whichever comes first in the DOM).

- [ ] **Step 7: Confirm the HTML still parses**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
python3 -c "
import html.parser
class P(html.parser.HTMLParser):
    def error(self,m): raise SystemExit('PARSE ERROR: '+m)
P().feed(open('index.html',encoding='utf-8').read()); print('parses OK')
"
```
Expected: `parses OK`

- [ ] **Step 8: Test the router link and the jump link in the browser**

Run: `open index.html`.

From the "I want to…" router on the landing view, click "…work out which tool fits the
task." Expected: switches to Using AI and scrolls to the **Tool Finder** (not the
PennChat-model box).

Click the **Using AI** tab directly, then its in-tab jump bar's "Tool Finder ↓" link.
Expected: scrolls to the same place.

**Deep-link and scroll behavior must be checked against the deployed site, not this
local `file://` preview** — this codebase's own convention notes that `location.hash`
assignment and `scrollIntoView` can behave differently in a sandboxed preview than on
GitHub Pages. A local check that looks right is a good sign but not sufficient; do the
same two clicks again once Task 5 pushes the branch and Task 5's PR is up, against the
Pages preview or `resources.pennai.law` directly if this ships.

- [ ] **Step 9: Commit**

```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
git add index.html
git commit -m "content: point #which-model at the new Tool Finder

The router's '...work out which tool fits the task' link and the
Using AI jump bar's featured link both already pointed at #which-model
— they were just wired to the wrong box. Renames the old PennChat-
model heading to #which-pennchat-model and gives the Tool Finder the
canonical id, so both existing links now land on the right content
with no href changes.

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>"
```

---

### Task 5: Whole-page verification and pull request

**Files:**
- Modify: none.

**Interfaces:**
- Consumes: Tasks 1–4.
- Produces: a PR against `pennai-law/penn-law-ai-resources`.

- [ ] **Step 1: Confirm no duplicate ids anywhere in the file**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
python3 - <<'PY'
import re
s = open('index.html', encoding='utf-8').read()
ids = re.findall(r'\bid="([^"]+)"', s)
dupes = sorted({i for i in ids if ids.count(i) > 1})
print("duplicate ids:", dupes if dupes else "none — PASS")
PY
```
Expected: `duplicate ids: none — PASS`

- [ ] **Step 2: Confirm the HTML parses and the JS is syntactically valid**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
python3 -c "
import html.parser
class P(html.parser.HTMLParser):
    def error(self,m): raise SystemExit('PARSE ERROR: '+m)
P().feed(open('index.html',encoding='utf-8').read()); print('HTML parses OK')
"
node -e "
const fs = require('fs');
const s = fs.readFileSync('index.html', 'utf8');
const m = s.match(/<script>([\s\S]*)<\/script>\s*<\/body>/);
new Function(m[1]);
console.log('JS syntax OK');
" 2>/dev/null || echo "node not available — rely on the browser check in Step 3"
```

- [ ] **Step 3: Full visual pass, all five tabs**

Run: `open index.html`. Walk every tab:

- **Getting Started:** Legal-Specific Tools grid shows five cards including
  CourtListener; layout doesn't break at two-per-row.
- **Using AI:** Tool Finder renders above "Which model should I use?"; "AI for
  Research" renders below both, before "Which Claude model, on a paid plan?"; the
  in-tab jump bar reads "Tool Finder ↓" not "Model Picker ↓".
- **Agentic & Advanced, Policies, AI at Penn:** unchanged — confirm nothing shifted
  here (this plan touches no content in these tabs directly, but Task 1's JS insertion
  sits in shared script; a stray syntax error there would break every tab's JS, not
  just Using AI's).
- `Cmd+K` search for `tool finder` and for `courtlistener` both return results (proves
  the new copy is in `h3`/`p`/`li` tags, which `performSearch` walks — bare `<div>`
  text wouldn't show up here).

- [ ] **Step 4: Check the narrow viewport**

Resize the browser below 400px wide. Expected: the Tool Finder's two fieldsets stack to
one column, chips wrap onto multiple lines without overflowing, verb-rows stack label
above detail, and the page body never scrolls horizontally.

- [ ] **Step 5: Review the full diff**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
git diff origin/main -- index.html | head -300
```

Read every hunk. Confirm nothing outside the four tasks' planned edits changed — no
reflowed whitespace, no stray entity conversions, no accidental edits to unrelated
cards.

- [ ] **Step 6: Open the pull request**

```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
git push -u origin nav/research-tool-finder
gh pr create --repo pennai-law/penn-law-ai-resources \
  --title "Add a task-based Tool Finder and AI for Research section" \
  --body "$(cat <<'EOF'
The router's "…work out which tool fits the task" link has always landed on "Which
model should I use?" — a box that only ever answers which *PennChat* model to pick. It
never touched Harvey vs. Legora vs. Westlaw vs. Claude.ai vs. the agentic tools. This
closes that gap.

## Changes
- **Tool Finder** (Using AI tab, above the existing PennChat-model box, which stays):
  pick a task and a Penn data-risk tier, get a ranked recommendation across the whole
  roster, deep-linking to each tool's existing card.
- **AI for Research** (same tab, below the finder): the same tools reframed by research
  verb — Find it / Verify it / Synthesize it / Analyze it — for research work that
  doesn't fit "draft" or "review."
- **New CourtListener card** (Getting Started → Legal-Specific Tools): free, public
  case-law/docket search had no card and nothing for the finder's public-research
  result to point at. No Penn risk badge assigned — see the design doc for why.
- **`#which-model` now points at the Tool Finder**, not the old box (renamed to
  `#which-pennchat-model`). Both existing links that used this anchor (the router, the
  Using AI jump bar) needed no `href` change, just a label update.

## Risk-tier assumptions to confirm before merge
Read off each tool's existing card badge, with one interpretive call — see "Assumptions
to verify before publishing" in the design doc for the full list. The one most likely
to be wrong: **Westlaw AI-Assisted Research and Lexis+ AI are treated as available at
all three risk tiers** in the finder, because their actual badge ("Legal Research
Terms") is a different category from Penn's Low/Moderate/High classification entirely
— this maps it to "always shows," which is a judgment call, not a fact read off a badge.

## Prompted by
Reviewing DDDI's `ai-resources-penn-sas` site (built for SAS, explicitly modeled on
this portal), which has an interactive tool-finder this portal lacked.

Design: `docs/superpowers/specs/2026-09-14-research-tool-finder-design.md`

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

---

## Self-Review

**Spec coverage.** All four spec "Changes" sections map to tasks: Tool Finder → Task 1;
AI for Research → Task 2; CourtListener card → Task 3; id re-anchor → Task 4. The
spec's "Assumptions to verify" list is carried into the PR body (Task 5, Step 6) so it
surfaces at review, matching how the precedent PR (`2026-08-03-faculty-status-access-
distinction`) surfaced its own assumptions.

**Additions beyond the spec.** None — the CourtListener "Free & Public" badge and the
Legal-Specific Tools intro-sentence edit are both named in the spec's Decisions and
Changes sections, not introduced fresh here.

**Placeholder scan.** No TBDs. Every content step gives an exact find-and-replace pair;
every verification step gives a runnable command with an expected result.

**Consistency check.** `id="card-courtlistener"` is created in Task 3 and referenced
identically in Task 1's `FINDER_TOOLS.research` (added by Task 3, Step 3) and Task 2's
"Find it" row (updated by Task 3, Step 4) — all three spellings match. `id="which-
model"` is removed from the old heading and added to the new one in the same task
(Task 4, Steps 2–3), so no intermediate commit leaves the anchor dangling. The chip
active-state class (`chip-active`) is defined in Task 1's CSS and toggled by Task 1's
JS with matching selectors (`.chip-row .chip`, `.chip.chip-active`).

**Known fragility.** Task 1 Step 5's JS insertion point (`goToTab`'s closing brace) was
verified against the file directly. Task 4's id swap is the one edit in this plan where
getting Steps 2 and 3 backwards would silently work in the browser (whichever `<h3>`
kept `id="which-model"` would just be the wrong one) rather than throwing an error —
Task 4 Step 6's duplicate-id check catches a *failure* to swap, but not a swap in the
wrong direction. Step 8's manual click-through is what actually catches that: if
"…work out which tool fits the task" scrolls to "Which model should I use?" instead of
the Tool Finder, Steps 2 and 3 were applied to the wrong headings.
