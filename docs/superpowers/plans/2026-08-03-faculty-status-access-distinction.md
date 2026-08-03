# Full-Time vs. Adjunct Faculty Access Distinction — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Distinguish full-time from adjunct faculty throughout the AI Resources portal, so adjunct faculty can see what they actually have access to, how to request Harvey, and why their own paid subscriptions must not touch student records.

**Architecture:** Four surgical edits to a single file, `index.html`. The access table gains a column; a new callout box lands beneath it; per-tool card prose and the Policies tab are corrected so they cannot drift from the table. No new files, no build step, no JS changes.

**Tech Stack:** Static HTML with inline CSS/JS. Served by GitHub Pages from `main`, root `/`. No build, no test framework, no dependencies beyond Google Fonts.

## Global Constraints

- **Only `index.html` is modified.** No other page, no assets, no CSS/JS files.
- **Column labels are exactly `Full-Time Faculty` and `Adjunct Faculty`.** No legend defining the categories — readers know which box they are in. This was an explicit instruction.
- **Glyph vocabulary:** `✓` available now · `◐` in pilot / available August 2026 · `○` by request to Penn Law ITS · `—` not provided by Penn Law. `○` is the only new glyph. **Do not introduce a `?` glyph.**
- **Literal Unicode characters**, not HTML entities, for glyphs — the file already uses literal `✓`, `◐`, `○`, `—` in table cells. Match that.
- **Voice is Polk Wagner's.** Direct, active, collegial. No hedging. Banned: "it's worth noting", "leverage", "utilize", "ensure", "robust", "stakeholders", "moving forward", "deep dive". Keep em-dashes to roughly one pair per paragraph.
- **FERPA is bridged into the page's existing vocabulary, not added beside it.** Say student records are FERPA-protected *and therefore* Moderate or High Risk. Do not build out a separate FERPA framework.
- **Never state a fact not in this plan.** Four access facts are assumptions carried from the spec (Copilot ✓, Westlaw/Lexis ✓, Zoom ✓ for adjuncts; Claude.ai — for adjuncts). Implement them as written; do not research or "correct" them mid-task.
- **The search index is live-DOM.** `performSearch` (`index.html:2527`) walks `h2, h3, p, li` inside `.tab-pane` at query time. New content is indexed automatically — but only if it uses those tags. Put callout copy in `<h3>`, `<p>`, and `<li>`, never bare `<div>` text.
- **Any `id` inside a pane is deep-linkable.** `activateFromHash` resolves the owning tab via `closest('.tab-pane')`. No routing table to update.

---

### Task 1: Split the access table into Full-Time and Adjunct columns

**Files:**
- Modify: `index.html:1198` (header row), `index.html:1201-1211` (tbody), `index.html:1214` (glyph note)

**Interfaces:**
- Consumes: nothing.
- Produces: a seven-column `.access-table`. Task 2's callout is written to explain the `○` and `◐` cells this task creates; Tasks 3 and 4 correct prose to match these cell values.

- [ ] **Step 1: Confirm the current table is the expected six-column form**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
grep -c '<th class="c">Faculty</th>' index.html
```
Expected: `1`

If it prints `0`, stop — the file is not in the state this plan was written against.

- [ ] **Step 2: Replace the header row**

Find this exact line (`index.html:1198`):

```html
          <tr><th>Tool</th><th class="c">Faculty</th><th class="c">1Ls</th><th class="c">2L&nbsp;/&nbsp;3L&nbsp;/&nbsp;LLM</th><th class="c">Staff</th><th>Use case</th></tr>
```

Replace with:

```html
          <tr><th>Tool</th><th class="c">Full-Time<br>Faculty</th><th class="c">Adjunct<br>Faculty</th><th class="c">1Ls</th><th class="c">2L&nbsp;/&nbsp;3L&nbsp;/&nbsp;LLM</th><th class="c">Staff</th><th>Use case</th></tr>
```

The `<br>` keeps the two-word headers from widening the table; `thead th` is already 12.5px, so two lines sit comfortably.

- [ ] **Step 3: Replace the ten body rows**

Replace the entire block from `<tr><th><a href="#card-pennchat"` through the `Zoom AI Companion` row (`index.html:1201-1211`) with the following. Each row gains exactly one `<td class="c">` immediately after the existing Faculty cell; every other cell and all "Use case" text is unchanged.

```html
          <tr><th><a href="#card-pennchat" style="color:var(--navy);">PennChat</a></th><td class="c">◐</td><td class="c">◐</td><td class="c">◐</td><td class="c">◐</td><td class="c">◐</td><td>Secure chat access to Claude and GPT models; approved for most University data. No web search.</td></tr>
          <tr><th><a href="#card-claude" style="color:var(--navy);">Claude.ai</a></th><td class="c">✓</td><td class="c">—</td><td class="c">◐</td><td class="c">—</td><td class="c">◐</td><td>General chat, drafting, and summarizing; customizable via skills and plugins. Anthropic models.</td></tr>
          <tr><th><a href="#card-chatgpt" style="color:var(--navy);">ChatGPT EDU</a></th><td class="c">✓</td><td class="c">—</td><td class="c">—</td><td class="c">—</td><td class="c">✓</td><td>General chat, drafting, and summarizing; customizable via custom GPTs. OpenAI models.</td></tr>
          <tr><th><a href="#card-claude-code" style="color:var(--navy);">Claude Code</a></th><td class="c">✓</td><td class="c">—</td><td class="c">◐</td><td class="c">—</td><td class="c">◐</td><td>Agentic coding and file workflows; most powerful (but least intuitive) way to access AI models; highly customizable.</td></tr>
          <tr><th><a href="#card-claude-cowork" style="color:var(--navy);">Claude Cowork</a></th><td class="c">✓</td><td class="c">—</td><td class="c">—</td><td class="c">—</td><td class="c">◐</td><td>App-based agent tool; allows access to local files; most of the power of Code with an easier interface; highly customizable.</td></tr>
          <tr><th><a href="#card-copilot" style="color:var(--navy);">Microsoft Copilot</a></th><td class="c">✓</td><td class="c">✓</td><td class="c">✓</td><td class="c">✓</td><td class="c">✓</td><td>AI drafting and answers inside your Office apps.</td></tr>
          <tr><th><a href="#card-harvey" style="color:var(--navy);">Harvey</a></th><td class="c">✓</td><td class="c">○</td><td class="c">—</td><td class="c">✓</td><td class="c">✓</td><td>Legal research, drafting, and other law-practice-specific tools.</td></tr>
          <tr><th><a href="#card-legora" style="color:var(--navy);">Legora</a></th><td class="c">◐</td><td class="c">◐</td><td class="c">—</td><td class="c">◐</td><td class="c">◐</td><td>Legal research, drafting, and other law-practice-specific tools.</td></tr>
          <tr><th><a href="#card-westlaw" style="color:var(--navy);">Westlaw AI</a> / <a href="#card-lexis" style="color:var(--navy);">Lexis+ AI</a></th><td class="c">✓</td><td class="c">✓</td><td class="c">✓</td><td class="c">✓</td><td class="c">—</td><td>AI legal research inside Westlaw / Lexis.</td></tr>
          <tr><th><a href="#card-zoom" style="color:var(--navy);">Zoom AI Companion</a></th><td class="c">✓</td><td class="c">✓</td><td class="c">✓</td><td class="c">✓</td><td class="c">✓</td><td>Meeting summaries, recaps, and action items.</td></tr>
```

- [ ] **Step 4: Add the `○` glyph to the note line**

Find this fragment in `index.html:1214`:

```html
<strong>◐</strong> in pilot / available in August 2026 &nbsp;·&nbsp; <strong>—</strong> not provided by Penn Law.
```

Replace with:

```html
<strong>◐</strong> in pilot / available in August 2026 &nbsp;·&nbsp; <strong>○</strong> by request to Penn Law ITS &nbsp;·&nbsp; <strong>—</strong> not provided by Penn Law.
```

- [ ] **Step 5: Verify every row has exactly seven cells**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
python3 - <<'PY'
import re, html.parser

class T(html.parser.HTMLParser):
    def __init__(self):
        super().__init__(); self.inTable=False; self.rows=[]; self.n=0
    def handle_starttag(self, tag, attrs):
        d=dict(attrs)
        if tag=='table' and 'access-table' in d.get('class',''): self.inTable=True
        elif self.inTable and tag=='tr': self.n=0
        elif self.inTable and tag in ('td','th'): self.n+=1
    def handle_endtag(self, tag):
        if tag=='table' and self.inTable: self.inTable=False
        elif self.inTable and tag=='tr': self.rows.append(self.n)

p=T(); p.feed(open('index.html',encoding='utf-8').read())
print("cells per row:", p.rows)
bad=[i for i,n in enumerate(p.rows) if n!=7]
print("FAIL rows with != 7 cells:", bad if bad else "none — PASS")
PY
```

Expected: `cells per row: [7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7]` (1 header + 10 body) and `none — PASS`.

- [ ] **Step 6: Verify the glyph counts changed as intended**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
sed -n '/<tbody>/,/<\/tbody>/p' index.html | grep -o '○' | wc -l
```
Expected: `1` (Harvey only)

- [ ] **Step 7: Open the page and look at the table**

Run: `open index.html`

Check by eye: seven columns, headers wrap to two lines cleanly, `○` appears once in the Harvey row, the note line under the table lists four glyphs. Narrow the window below ~700px and confirm the table scrolls horizontally inside its wrapper rather than pushing the page sideways.

- [ ] **Step 8: Commit**

```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
git add index.html
git commit -m "content: split access table into full-time and adjunct faculty columns

Adds an Adjunct Faculty column and a ○ glyph for by-request access
(Harvey). Adjunct faculty have no research account, so the tools
provisioned that way now read — rather than ✓.

Co-Authored-By: Claude Opus 5 (1M context) <noreply@anthropic.com>"
```

---

### Task 2: Add the adjunct faculty callout box

**Files:**
- Modify: `index.html` — insert between the `.access-note` paragraph (ends line 1214) and the `General AI Tools` heading (line 1216)

**Interfaces:**
- Consumes: the `○` and `◐` cells from Task 1; the `goToCard(tabId, elementId)` helper already defined at `index.html:2469` and used at `index.html:1658`.
- Produces: `id="adjunct-faculty"` — Task 4 links to it from the Policies tab.

- [ ] **Step 1: Insert the callout**

Insert this block immediately after the closing `</p>` of the `.access-note` paragraph and immediately before `<h3 style="font-family:'EB Garamond',serif; font-size:1.35rem; color:var(--navy); margin-bottom:0.5rem; font-weight:500;">General AI Tools</h3>`:

```html
    <div id="adjunct-faculty" style="border-left:4px solid var(--navy); background:var(--cream); border-radius:0 10px 10px 0; padding:1.4rem 1.75rem; margin:0 0 2rem; scroll-margin-top:70px;">
      <div style="display:flex; align-items:center; gap:0.6rem; flex-wrap:wrap; margin-bottom:0.4rem;">
        <span style="font-size:10.5px; font-weight:700; letter-spacing:0.09em; text-transform:uppercase; color:#fff; background:var(--navy); padding:0.22rem 0.65rem; border-radius:999px;">Adjunct Faculty</span>
        <h3 style="margin:0; font-family:'EB Garamond',serif; font-size:1.3rem; color:var(--navy); font-weight:500;">What&rsquo;s different for you</h3>
      </div>
      <p style="margin:0 0 0.9rem;">Most of Penn Law&rsquo;s AI licensing runs through faculty research accounts. Adjunct faculty don&rsquo;t have one, so Claude.ai, ChatGPT EDU, Claude Code, and Claude Cowork aren&rsquo;t provisioned for you through the law school. Here&rsquo;s what is:</p>
      <ul style="margin:0 0 0.9rem;">
        <li><strong>Harvey is available on request.</strong> Ask <a href="https://www.law.upenn.edu/its/docs/ai/harvey-ai/" target="_blank" rel="noopener" style="color:var(--navy); text-decoration:underline;">Penn Law ITS</a> for access and log in with your LawKey username.</li>
        <li><strong>Microsoft Copilot, Westlaw AI, Lexis+ AI, and Zoom AI Companion</strong> come with your Penn Carey Law accounts. Nothing to request.</li>
        <li><strong>PennChat eligibility is still being settled.</strong> Adjunct access hasn&rsquo;t been confirmed ahead of the enterprise launch. Check back after August.</li>
      </ul>
      <p style="margin:0;"><strong>If you use your own paid subscription instead, keep student material out of it.</strong> A personal Claude, ChatGPT, or Gemini account is fine for your own preparation, but it carries none of Penn&rsquo;s approvals. Student records &mdash; grades, exams, papers, evaluations &mdash; are protected under FERPA and sit at Moderate or High Risk under <a href="#" onclick="activateTab('policies'); window.scrollTo({top: document.querySelector('.tab-nav-wrap').offsetTop - 56, behavior:'smooth'}); return false;" style="color:var(--navy); text-decoration:underline;">Penn&rsquo;s data classification</a>. They don&rsquo;t belong in a tool you&rsquo;re paying for yourself.</p>
    </div>

```

- [ ] **Step 2: Verify the anchor and heading tags exist**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
grep -c 'id="adjunct-faculty"' index.html
grep -c 'What&rsquo;s different for you' index.html
```
Expected: `1` and `1`

- [ ] **Step 3: Verify the callout sits inside the Getting Started pane**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
python3 - <<'PY'
import re
s=open('index.html',encoding='utf-8').read()
gs=s.index('id="getting-started"')
nxt=s.index('class="tab-pane"', gs)
a=s.index('id="adjunct-faculty"')
print("inside getting-started pane:", gs < a < nxt)
PY
```
Expected: `inside getting-started pane: True`

This matters because `activateFromHash` resolves the owning tab with `closest('.tab-pane')`. If the callout landed outside the pane, the deep link would silently fail.

- [ ] **Step 4: Test the deep link in a browser**

Run: `open "index.html#adjunct-faculty"`

Expected: the page loads on the Getting Started tab and scrolls the callout into view, centered.

Then click the "Penn's data classification" link inside the callout. Expected: switches to the Policies tab and scrolls to the tab nav.

- [ ] **Step 5: Confirm the new copy is searchable**

Run: `open index.html`, press `Cmd+K`, type `adjunct`.

Expected: results appear from the Getting Started tab, including the callout's heading and body text. This confirms the copy is in `h3`/`p`/`li` tags rather than bare divs — `performSearch` only walks those.

- [ ] **Step 6: Commit**

```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
git add index.html
git commit -m "content: add adjunct faculty callout under the access table

Explains the research-account gap, the Harvey request path, unsettled
PennChat eligibility, and why personal subscriptions must not see
FERPA-protected student records.

Co-Authored-By: Claude Opus 5 (1M context) <noreply@anthropic.com>"
```

---

### Task 3: Correct tool-card prose so it matches the table

**Files:**
- Modify: `index.html:1240` (Claude.ai), `:1250` (ChatGPT EDU), `:1260` (Gemini), `:1278` (Cowork), `:1290` (Claude Code), `:1308` (Harvey), `:1362` (Zoom), `:1478` (Agentic-tab Claude Code)

**Interfaces:**
- Consumes: the table values from Task 1; the `id="adjunct-faculty"` anchor from Task 2.
- Produces: nothing consumed downstream.

Each edit is a single clause. Do not rewrite surrounding sentences.

- [ ] **Step 1: Claude.ai card (`index.html:1240`)**

Find: `Faculty can obtain licenses via your research account;`

Replace with: `Full-time faculty can obtain licenses via a research account;`

- [ ] **Step 2: ChatGPT EDU card (`index.html:1250`)**

Find: `Available to faculty and staff via research or departmental budgets.`

Replace with: `Available to full-time faculty and staff via research or departmental budgets.`

- [ ] **Step 3: Google Gemini card (`index.html:1260`)**

Find: `Faculty can use research accounts.`

Replace with: `Full-time faculty can use research accounts.`

- [ ] **Step 4: Claude Cowork card (`index.html:1278`)**

Find: `Requires a paid Claude subscription; a limited PCL staff pilot begins in July.`

Replace with: `Requires a paid Claude subscription, which full-time faculty can fund through a research account; a limited PCL staff pilot begins in July.`

- [ ] **Step 5: Claude Code card, Getting Started tab (`index.html:1290`)**

Find: `It&rsquo;s the engine behind the Law Faculty Skills. Requires a paid Claude subscription.`

Replace with: `It&rsquo;s the engine behind the Law Faculty Skills. Requires a paid Claude subscription, which full-time faculty can fund through a research account.`

- [ ] **Step 6: Harvey card (`index.html:1308`)**

Find: `All upper-level students, full-time faculty, and staff have access. Log in with your LawKey username.`

Replace with: `All upper-level students, full-time faculty, and staff have access; <a href="#adjunct-faculty" onclick="return goToCard('getting-started','adjunct-faculty');" style="color:var(--navy);">adjunct faculty can request access</a> from Penn Law ITS. Log in with your LawKey username.`

- [ ] **Step 7: Zoom AI Companion card (`index.html:1362`)**

Find: `Available on full-time faculty, staff, and student Zoom accounts.`

Replace with: `Available on faculty, staff, and student Penn Zoom accounts.`

Rationale: the table marks Zoom `✓` for adjunct faculty, so the card's "full-time" qualifier is now wrong. Dropping the qualifier — rather than writing "full-time and adjunct" — keeps the sentence short and matches the row.

- [ ] **Step 8: Claude Code card, Agentic AI tab (`index.html:1478`)**

Find: `At Penn Law, faculty can use it via a research account.`

Replace with: `At Penn Law, full-time faculty can use it via a research account.`

This card duplicates the research-account claim from Step 5. Both must change or the two tabs contradict each other.

- [ ] **Step 9: Verify no unqualified research-account claims remain**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
grep -n 'research account' index.html | grep -iv 'full-time' | grep -v 'adjunct-faculty'
```
Expected: no output, apart from the callout paragraph from Task 2 which says "faculty research accounts" generically and is correct in context. If that line appears, confirm it reads `Most of Penn Law&rsquo;s AI licensing runs through faculty research accounts` and move on.

- [ ] **Step 10: Verify the Harvey card's cross-link works**

Run: `open index.html`, scroll to the Harvey card, click "adjunct faculty can request access".

Expected: stays on Getting Started and scrolls up to the callout.

- [ ] **Step 11: Commit**

```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
git add index.html
git commit -m "content: qualify research-account claims as full-time faculty

Card prose said 'faculty' where it meant full-time faculty. Corrects
Claude.ai, ChatGPT EDU, Gemini, Cowork, and both Claude Code cards;
adds the Harvey request path; drops the stale full-time qualifier
from Zoom.

Co-Authored-By: Claude Opus 5 (1M context) <noreply@anthropic.com>"
```

---

### Task 4: Update the Policies tab

**Files:**
- Modify: `index.html:1896` (Harvey), `:1898` (ChatGPT EDU), `:1899` (Claude), `:1900` (Zoom), and the *Personal-Account Use of Other Tools* paragraph following `index.html:1908`

**Interfaces:**
- Consumes: `id="adjunct-faculty"` from Task 2; `goToCard` from `index.html:2469`.
- Produces: nothing consumed downstream.

- [ ] **Step 1: Harvey entry in the ITS-reviewed list (`index.html:1896`)**

Find: `Available to full-time faculty, staff, and 2L/3L/LLM students (not 1Ls). LawKey login at app.harvey.ai.`

Replace with: `Available to full-time faculty, staff, and 2L/3L/LLM students (not 1Ls); adjunct faculty on request to Penn Law ITS. LawKey login at app.harvey.ai.`

- [ ] **Step 2: ChatGPT EDU entry (`index.html:1898`)**

Find: `Remains available to faculty and staff (students are moving to Claude for 2026&ndash;27); request access via Penn Law ITS.`

Replace with: `Remains available to full-time faculty and staff (students are moving to Claude for 2026&ndash;27); request access via Penn Law ITS.`

- [ ] **Step 3: Claude entry (`index.html:1899`)**

Find: `(faculty via research accounts; 1Ls via the 1L Legal Practice Skills curriculum)`

Replace with: `(full-time faculty via research accounts; 1Ls via the 1L Legal Practice Skills curriculum)`

- [ ] **Step 4: Zoom AI Companion entry (`index.html:1900`)**

Find: `Available to full-time faculty, staff, and students with a Penn Zoom account.`

Replace with: `Available to faculty, staff, and students with a Penn Zoom account.`

Matches the Zoom card change in Task 3, Step 7 and the `✓` in the table.

- [ ] **Step 5: Add the adjunct pointer to Personal-Account Use of Other Tools**

Find the closing sentence of that paragraph:

```html
Personal-account tools should never see Penn's Moderate or High Risk Data.
```

Replace with:

```html
Personal-account tools should never see Penn's Moderate or High Risk Data. This comes up most often for <a href="#adjunct-faculty" onclick="return goToCard('getting-started','adjunct-faculty');" style="color:var(--navy); text-decoration:underline;">adjunct faculty</a>, who have no research account and so are likeliest to be working on their own subscription &mdash; student records are FERPA-protected and sit in exactly that Moderate-to-High band.
```

Verified: that sentence uses a **literal ASCII apostrophe** in `Penn's`, not `&rsquo;`. Copy the find-string exactly as printed above. (Much of the surrounding page does use `&rsquo;`, so don't normalize it by reflex.)

- [ ] **Step 6: Verify all five edits landed**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
grep -c 'adjunct faculty on request to Penn Law ITS' index.html
grep -c 'Remains available to full-time faculty and staff' index.html
grep -c 'full-time faculty via research accounts' index.html
grep -c 'Available to faculty, staff, and students with a Penn Zoom account' index.html
grep -c 'who have no research account' index.html
```
Expected: `1` five times.

- [ ] **Step 7: Verify the Policies → Getting Started jump**

Run: `open index.html`, click the Policies tab, find *Personal-Account Use of Other Tools*, click the "adjunct faculty" link.

Expected: switches to the Getting Started tab and scrolls the callout into view. This exercises `goToCard` across tabs, which is the case most likely to break.

- [ ] **Step 8: Commit**

```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
git add index.html
git commit -m "content: align Policies tab with the full-time/adjunct distinction

Qualifies the ITS-reviewed list entries, adds Harvey's by-request path
for adjuncts, and points the personal-account guidance at the new
callout.

Co-Authored-By: Claude Opus 5 (1M context) <noreply@anthropic.com>"
```

---

### Task 5: Whole-page verification and pull request

**Files:**
- Modify: none.

**Interfaces:**
- Consumes: Tasks 1–4.
- Produces: a PR against `pennai-law/penn-law-ai-resources`.

- [ ] **Step 1: Confirm no contradictions survive**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
echo "--- every 'full-time' mention ---"
grep -o 'full-time [a-z]*' index.html | sort | uniq -c
echo "--- every 'adjunct' mention ---"
grep -oi 'adjunct[ a-z]\{0,10\}' index.html | sort | uniq -c
```

Read the output. Every "full-time" should be followed by "faculty". Every "adjunct" mention should be one you added. Nothing should say "full-time faculty" about Zoom.

- [ ] **Step 2: Confirm the HTML still parses**

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

- [ ] **Step 3: Full visual pass**

Run: `open index.html`

Walk all six tabs. Check specifically:
- Getting Started: table, note line, callout, and the tool cards below it all render in order with no layout break.
- The callout's navy left border and cream ground match the PennChat callouts in the Using AI tab.
- Policies: the ITS-reviewed list reads cleanly.
- `Cmd+K` search for `adjunct` and for `FERPA` both return results.

- [ ] **Step 4: Check the narrow viewport**

Resize the browser below 400px wide. Expected: the table scrolls inside `.access-wrap`; the callout reflows without overflow; the page body does not scroll horizontally.

- [ ] **Step 5: Review the full diff**

Run:
```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
git diff origin/main -- index.html
```

Read every hunk. Confirm nothing outside the planned edits changed — no reflowed whitespace, no stray entity conversions.

- [ ] **Step 6: Open the pull request**

```bash
cd "/Users/polkwagner/Penn Law Dropbox/Polk Wagner/code/penn-law-ai-resources"
git push -u origin content/faculty-status-access-distinction
gh pr create --repo pennai-law/penn-law-ai-resources \
  --title "Distinguish full-time from adjunct faculty access" \
  --body "$(cat <<'EOF'
Splits the portal's single "Faculty" column into **Full-Time Faculty** and **Adjunct Faculty**, and adds a callout explaining what adjunct faculty can actually get.

## Changes
- Access table: seven columns; new `○` glyph for "by request to Penn Law ITS" (Harvey only).
- New `#adjunct-faculty` callout under the table — research-account gap, Harvey request path, unsettled PennChat eligibility, and a FERPA caution on personal subscriptions.
- Card prose: research-account claims qualified as full-time faculty across Claude.ai, ChatGPT EDU, Gemini, Cowork, and both Claude Code cards.
- Policies tab: ITS-reviewed list aligned; personal-account guidance points at the callout.

## Access facts to confirm before merge
Four cells rest on assumption rather than confirmed fact:
- Microsoft Copilot ✓ for adjuncts (assumes a PCL O365 account)
- Westlaw AI / Lexis+ AI ✓ for adjuncts (assumes teaching credentials)
- Zoom AI Companion ✓ for adjuncts (contradicts the page's prior "full-time" phrasing)
- Claude.ai — for adjuncts (becomes ◐ if the ~August University agreement covers all faculty)

Design: `docs/superpowers/specs/2026-08-03-faculty-status-access-distinction-design.md`

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

---

## Self-Review

**Spec coverage.** All five spec sections map to tasks: table split → Task 1; new glyph → Task 1 Step 4; callout → Task 2; card prose → Task 3; Policies tab → Task 4. The spec's four "assumptions to verify" are carried into the PR body in Task 5 Step 6 so they surface at review rather than being lost.

**Additions beyond the spec.** Task 3 Step 3 corrects the Gemini card, which the spec did not list but which carries the same unqualified "Faculty can use research accounts" claim — leaving it would reproduce the drift the spec set out to close. Task 4 Steps 2–3 extend the same qualification to the Policies list's ChatGPT EDU and Claude entries for the same reason.

**Placeholder scan.** No TBDs. Every edit gives exact find-and-replace strings and every verification gives a runnable command with expected output.

**Consistency check.** `id="adjunct-faculty"` is defined in Task 2 and referenced identically in Task 3 Step 6 and Task 4 Step 5. `goToCard('getting-started','adjunct-faculty')` uses the two-argument signature the file already uses at `index.html:1658`. The Zoom change is made in three places — table (Task 1), card (Task 3 Step 7), Policies list (Task 4 Step 4) — and all three now say the same thing.

**Known fragility.** Task 4 Step 5's find-string was verified against the file — it uses a literal ASCII apostrophe, which the step now states. Task 1 Step 3 replaces ten lines wholesale, so a stale copy of the file would be caught by Step 1's guard.
