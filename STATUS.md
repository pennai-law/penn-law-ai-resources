# Penn Carey Law AI Resources — Status

**Updated:** 2026-09-11

Tool catalog compacted in Getting Started (`feature/compact-tool-cards`): all 12 cards now use a horizontal title + logo lockup and a unified badge row displaying explicit Data Risk badges (matching Penn ISC data classification: High, Mod, Low, or Personal) alongside access and mode tags.

Penn Law ITS announced purchasing details for Penn Enterprise Claude, now incorporated across `index.html` and `agentic-ai-security.html`:
- Standard ($16/mo): 112 msgs/5h rolling, $50/wk API allocation.
- Premium ($50/mo): 225 msgs/5h rolling, $250/wk API allocation, includes Fable 5.
- Both tiers require a fiscal-year commitment; Standard can upgrade to Premium during the year. Faculty purchase via research funds; staff via departmental funds (`itshelp@law.upenn.edu`).
- Claude Cowork is not currently available through Enterprise (under University review; staff pilot continues).
- Access table staff glyphs for Claude.ai and Claude Code updated to ◆.
- Model guidance (`#which-model-claude`) and institutional policy listings updated.

- Claude.ai and ChatGPT aligned on equal footing across access table, cards, and policy/security sections: both offer Enterprise (via ITS with research funds; Low & Mod clearance) or personal subscriptions (Plus/Pro, Pro/Max; Low Risk only) with research funds. Staff licenses for both funded via departmental budgets.

- Structural and flow improvements implemented on `feature/structural-improvements`:
  - **Inverted Getting Started**: Orientation prose (*"What Can AI Actually Do?"*) moved to the top of the pane with `#orientation`, followed by the Access Table, Adjunct Faculty notice, and categorized tool catalog.
  - **Deduplicated Tool Cards**: Canonical technical cards established in *Agentic & Advanced* for Claude Code (`#card-ag-claude-code`) and Claude Cowork (`#card-ag-claude-cowork`), with clean handoff CTAs from compact identity cards in *Getting Started*.
  - **Per-Tab In-Page Jump Lists**: Added `.tab-jump-bar` single-line scrollable pill nav across all five tabs, with intelligent scroll alignment in `activateFromHash` and jump-click interception (`block: 'start'` with `scroll-margin-top: 70px` for headings; `block: 'center'` for cards).

- Claude Code access updated across the portal (`feature/1l-claude-code`): 1Ls are provisioned with Claude Code as part of Penn Enterprise Claude. Access table 1Ls column updated to ✓ (matching Claude.ai), `#card-claude-code` badge updated to `◆ Enterprise via ITS · Fundable`, and card/policy descriptions updated.
- Hero metadata and footer updated (`fix/hero-meta-date-and-audience`): exact date set to September 11, 2026, and audience note clarified for student- or staff-specific guidance linking to Penn Law ITS.

**Where it stands:** Hero metadata and Claude Code 1L provisioning updated and verified. Ready for PR and merge.

**Next:** Remaining review items: visible search field. Update July ITS review stamp when ITS re-reviews.

**Open:** At 375px only two tabs fit and the header wordmark wraps to three lines. Unconfirmed cells: Legora for adjuncts (○ or ✓), Westlaw/Lexis for staff (—). Eddie's lessons.md calibration is still to paste.

