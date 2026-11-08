# salesforce-safe-delivery

**A Claude Code skill for making AI-assisted Salesforce changes safe on live orgs.**

> AI can write Salesforce code fast. In a real org the risk isn't the code. It's deploying over someone else's change, firing automation you didn't know about, or touching production without a plan. This skill writes down the guard rails I use when working with Claude Code on a live, multi-team Salesforce org, so Claude follows them every time.

## 30-second start
**Option 1: Claude Code skill.** Copy this folder to `~/.claude/skills/salesforce-safe-delivery/`. Claude Code loads it automatically when you work on Salesforce changes.

**Option 2: any AI coding assistant.** Paste the contents of [`CLAUDE-snippet.md`](CLAUDE-snippet.md) into your project's `CLAUDE.md` (or your tool's rules file).

**Option 3: no AI.** Use the [`checklists/`](checklists/) folder as your own working checklists: production release, impact analysis, data migration, integrations and cloud-specific notes.

## Why I built this
I lead Salesforce delivery on government modernization projects, including Public Sector Solutions work and custom applications with external portals. Working with Claude Code, the code came quickly; the near-misses came from the org:
- a deploy from an outdated copy of a shared navigation component silently removed features other teams had shipped;
- a portal-only import blanked an internal screen;
- a one-field update would have cascaded through a trigger to hundreds of records and re-sent emails.

These rules come from those lessons.

## What it makes Claude do
- **Fit how your team deploys.** In pipeline/CAB teams (the default, typical in government), Claude is the **release preparer**: PR, impact analysis and a ready-to-submit CAB release note. It never deploys to production. Small teams without a pipeline can let it deploy after an explicit GO.
- **Use standard features first.** For each requirement it shows the out-of-the-box, declarative and Salesforce-product options before suggesting custom Apex or LWC, and says why custom is needed.
- **Treat production as read-only:** queries only. In pipeline teams Claude never deploys; in direct-CLI teams every deploy shows components, diff and test level, then waits for your GO.
- **Verify live, every time:** production, then sandbox, then local. Never assume git matches the org.
- **Protect shared components:** build on the org's live source, additions only, and re-check before deploying.
- **Build in the sandbox first**, preferring additive changes ("hide, don't remove").
- **Plan for both internal and portal users.**
- **Map the impact:** triggers, flows, validation rules and email cascades, before release.
- **Pass release gates:** validation, a cascade pre-check, a data snapshot and a written rollback, then a CAB release note (pipeline) or validate + quick deploy (direct CLI).
- **Stop on surprises** and report instead of working around them.

## Contents
| File | Purpose |
|---|---|
| `SKILL.md` | The rules and the workflow |
| `checklists/prod-release.md` | Production release: shared gates, then Mode A (pipeline/CAB hand-off) or Mode B (direct CLI), and rollback |
| `checklists/cab-release-note.md` | Change advisory board (CAB) release note template, filled in for every pipeline release |
| `checklists/impact-analysis.md` | How to find what a change will set off |
| `checklists/data-migration.md` | Moving data from a legacy app or spreadsheets: matching keys, preview, snapshot, reconciliation |
| `checklists/integrations.md` | Standard-first integration choices (e.g. Einstein Activity Capture), licensing, security, leftover-code trap |
| `checklists/cloud-specific-notes.md` | Extra checks for Service, Sales and Experience Cloud and custom platform apps |
| `CLAUDE-snippet.md` | The rules as a paste-in block for `CLAUDE.md` or other AI tools |
| `examples/walkthrough.md` | An anonymized real-world delivery |

## Who it's for
Salesforce admins, developers and release leads using Claude Code on orgs with real users, especially orgs with several teams, cherry-picked deploys, or Experience Cloud portals. Works with pipeline and change-board processes; Claude prepares the release evidence and your pipeline deploys.

## What it isn't
It doesn't replace your org's change management, security review or testing. It makes the AI follow them.

## License
MIT. See `LICENSE`.
