# Penn Carey Law AI Resources — Status

**Updated:** 2026-08-18

The 2026-08-17 AI office hours produced a work queue, and this session cleared its first item. Two faculty couldn't find skills that already existed, and the cause was worse than it looked: `claude-skills.html` — the full twelve-skill catalog — was linked from nowhere in `index.html`, and "multiple choice" appeared zero times, so Cmd+K returned nothing. The install path they'd land on after clicking through was broken too. The `law-faculty-skills` README led with `/install-skill`, which is not a Claude Code command, above a `cp` that assumed an unmentioned `git clone`. Both are fixed, merged, and verified live.

Scrubbing the Advanced Claude tab against Anthropic's live docs then exposed a recurring pattern worth watching for: the page kept documenting the mechanism an engineer would reach for rather than the one a faculty reader would — "drop a file called `CLAUDE.md`" (it's `/init`), "commit it into your git repository" (their folder is already in Dropbox), skills as slash commands only (Claude loads them on its own).

**Where it stands:** Deployed and verified at resources.pennai.law. `main` is clean and level with origin; PR #20 here and PR #1 on `law-faculty-skills` are both merged.

**Next:** Two office-hours items remain — the Penn-vs-personal Claude comparison (Cowork absent, no education discount, indemnification is the real difference) and a short FERPA block. Add Legora to the access table once the contract is signed.

**Open:** The ITS review stamp still reads July 2026; it moves when ITS re-reviews, not on edit. A `.claude-plugin/marketplace.json` in `law-faculty-skills` would give a genuine one-command install, left unshipped because it couldn't be tested end to end. After editing `CLAUDE.md`, regenerate `AGENTS.md` with `~/.claude/scripts/sync-agents-md`.
