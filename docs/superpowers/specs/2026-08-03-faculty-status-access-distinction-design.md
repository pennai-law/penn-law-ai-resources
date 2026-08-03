# Design: Full-Time vs. Adjunct Faculty Access Distinction

**Date:** 2026-08-03
**File touched:** `index.html` (only)

## Goal

The portal currently addresses "faculty" as one undifferentiated population. It isn't
one. Full-time faculty hold research accounts and are provisioned the tools that run
through them; adjunct faculty are not, and several tools they see marked ✓ today are
in fact unavailable to them. The page should say which is which, tell adjuncts what
they *can* get, and warn them about the FERPA exposure that comes with using their
own subscriptions instead.

## Decisions

**Terminology: "Full-Time Faculty" and "Adjunct Faculty."** No defining legend — the
categories are self-evident to the reader, and "full-time faculty" is already the
page's phrasing in three places (Harvey card, Zoom card, the ITS-reviewed list). This
also avoids "standing faculty," which is precise about tenure track but wrong about
access: practice professors and clinical faculty are not standing faculty yet do hold
research accounts.

**Structure: split table column *and* a callout box.** The access table answers "can I
get X?"; the callout answers "why not, and what do I do instead?" Neither alone does
both.

**FERPA is bridged into the page's existing vocabulary, not added alongside it.** The
portal frames sensitivity entirely through Penn's Data Risk Classification
(Low/Moderate/High); "FERPA" appears nowhere today. The callout says student records
are FERPA-protected *and therefore* Moderate or High Risk — teaching the mapping
rather than introducing a second framework faculty must hold in mind.

## Changes

### 1. Access table (`index.html:1196`)

Split `<th class="c">Faculty</th>` into two columns: `Full-Time Faculty`, `Adjunct
Faculty`. Seven columns total. The table already sits inside `.access-wrap`
(`overflow-x:auto`), so narrow viewports scroll rather than break.

| Tool | Full-Time | Adjunct |
|---|---|---|
| PennChat | ◐ | ◐ |
| Claude.ai | ✓ | — |
| ChatGPT EDU | ✓ | — |
| Claude Code | ✓ | — |
| Claude Cowork | ✓ | — |
| Microsoft Copilot | ✓ | ✓ |
| Harvey | ✓ | ○ |
| Legora | ◐ | ◐ |
| Westlaw AI / Lexis+ AI | ✓ | ✓ |
| Zoom AI Companion | ✓ | ✓ |

Existing values for the 1L / 2L-3L-LLM / Staff columns are unchanged.

### 2. One new glyph

`○` = *by request to Penn Law ITS*, appended to the existing ✓ / ◐ / — note line at
`index.html:1214`. Used only for Harvey.

No `?` glyph. PennChat and Legora are already `◐` for every population (pilot /
arriving August), so adjunct uncertainty on those two rows rides on the glyph that is
already there; the callout carries the caveat in words. Inventing a `?` would imply
the ambiguity is adjunct-specific when it is schedule-specific.

### 3. Callout box

Placed directly below the access table and its note, above the "General AI Tools"
heading. Styled to match the existing bordered callouts in the Using AI tab
(`border-left:4px solid var(--navy)`, `background:var(--cream)`, `border-radius:0 10px
10px 0`, `scroll-margin-top:70px`). Carries `id="adjunct-faculty"` — per the portal's
routing, any `id` inside a pane is deep-linkable with no routing-table update, because
`activateFromHash` resolves the owning tab via `closest('.tab-pane')`.

Four beats, in Polk's voice, short:

1. Adjunct faculty do not have research accounts, so the tools provisioned that way —
   Claude.ai, ChatGPT EDU, Claude Code, Claude Cowork — are not available through Penn
   Law.
2. Harvey is available to adjunct faculty on request to Penn Law ITS.
3. PennChat eligibility for adjunct faculty is being confirmed ahead of the enterprise
   launch.
4. Using your own paid subscription is fine for your own work but is not approved for
   Penn data. Student records — grades, exams, evaluations — are FERPA-protected and
   sit at Moderate or High Risk. Do not put them into a personal-account tool.

Beat 4 links to the Policies tab via the existing `activateTab('policies')` pattern.

### 4. Card prose

One clause each, only where a card currently says "faculty" unqualified and the
distinction changes the answer:

- **Claude.ai** (`:1240`) — research-account licensing is full-time faculty only.
- **ChatGPT EDU** (`:1250`) — same.
- **Claude Code** (`:1290`) and **Claude Cowork** (`:1278`) — same.
- **Harvey** (`:1308`) — already says "full-time faculty"; add that adjunct faculty can
  request access through Penn Law ITS.
- **Zoom AI Companion** (`:1362`) — currently reads "full-time faculty, staff, and
  student Zoom accounts," which implies adjuncts are excluded. Correct to include
  adjunct faculty.

The Agentic AI tab's Claude Code card (`:1478`) carries the same research-account
claim and gets the same clause, so the two do not drift.

### 5. Policies tab

One sentence added to *Personal-Account Use of Other Tools* (`index.html:1908`) naming
adjunct faculty as the population most often operating on personal accounts, pointing
back to `#adjunct-faculty`. That section already states the Moderate/High Risk
prohibition — this is a pointer, not a new rule.

The ITS-reviewed list above it (`:1896`, `:1899`, `:1900`) repeats "full-time faculty"
for Harvey and Zoom; Harvey's entry gains the by-request note and Zoom's is corrected,
matching the cards.

## Assumptions to verify before publishing

Approved on my stated assumptions, not on confirmed fact. If any is wrong the table
cell and matching card prose both change:

- **Microsoft Copilot ✓ for adjuncts** — assumes adjunct faculty receive a PCL O365
  account, which is what the Microsoft enterprise agreement rides on.
- **Westlaw AI / Lexis+ AI ✓ for adjuncts** — assumes teaching adjuncts receive
  academic credentials under the law school's subscriptions.
- **Zoom AI Companion ✓ for adjuncts** — contradicts the page's current "full-time
  faculty, staff, and students" phrasing, which may itself be imprecise rather than
  exclusionary.
- **Claude.ai — for adjuncts** — if the University-wide Claude agreement expected
  ~August 2026 covers all faculty rather than research-account holders, this becomes ◐
  and beat 1 of the callout needs rewording.

## Out of scope

- No restyling, no new tab, no change to the tool cards' layout or the tab structure.
- Staff guidance stays out — Penn Law ITS owns it, per the portal's faculty-only scope.
- Other faculty categories (visiting, emeritus) are not separately addressed; the
  two-column split is the distinction that maps to provisioning.
