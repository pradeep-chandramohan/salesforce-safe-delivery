---
name: salesforce-safe-delivery
description: Use when making ANY change to a Salesforce org with Claude Code (Apex, LWC, flows, metadata, data) — especially when a production org is involved. Enforces sandbox-first delivery, live-org verification, impact analysis, and explicit human approval for every production action.
---

# Salesforce Safe Delivery

AI writes Salesforce code quickly. In a real org the danger is rarely the code itself. Changes go wrong because:
- a deploy overwrites someone else's work;
- the change sets off automation nobody knew about;
- someone touches production without a plan.

This skill holds Claude to the habits of a careful release lead.

## Deployment modes (decide first)

| | **Mode A: pipeline / CAB (default)** | **Mode B: direct CLI** |
|---|---|---|
| Typical team | Regulated programs and enterprises: Git branches, pull requests, a pipeline (Copado, Gearset, DevOps Center, Azure DevOps, GitHub Actions), SIT → UAT → Staging, a change advisory board | Small agencies, nonprofits, single-admin orgs, small consulting engagements with no pipeline |
| Claude's role | **Release preparer.** Builds and tests in the sandbox, verifies live, maps the impact, prepares the branch/PR and the **CAB release note**. **Never deploys to, or runs DML in, production** | **Release operator.** Same preparation, then validates and quick-deploys to production, each step after the user's explicit GO |
| Who deploys to prod | The pipeline or the release manager, after approval (separation of duties) | Claude, after a GO |

**How to pick the mode:**
1. Look for pipeline files: `.github/workflows/`, `azure-pipelines.yml`, `.gitlab-ci.yml`, Copado or Gearset configuration.
2. Look for what the user has stated, e.g. "we go through CAB" in `CLAUDE.md`.
3. **If unsure, ask, and default to Mode A.** The safe default is that Claude doesn't deploy to production.

## The rules (non-negotiable)

1. **Standard first: show out-of-the-box options before custom.** For every requirement, present the options in this order, with trade-offs, and let the user choose before building:
   1. **Standard features and configuration:** standard objects, page layouts, reports and dashboards, list views, validation rules, assignment and sharing rules.
   2. **Declarative automation:** Flow, approval processes, email alerts.
   3. **Salesforce-provided features and products:** e.g. Einstein Activity Capture for email/calendar sync, industry clouds, AppExchange packages. Check the licensing.
   4. **Custom code (Apex, LWC) only when the options above can't meet the need.** State why in one line. For example: "Flow cannot parse an uploaded Excel file, so a small LWC is needed."

   Never build custom integration code for something a standard feature already does. If custom leftovers exist in an org, don't deploy them without confirming they're still the chosen approach.
2. **Production is read-only by default.**
   - Reading is allowed without a GO: metadata, Tooling API queries, record counts and record IDs, and reading live source through the Tooling API, saved to a separate folder (e.g. `prod-live/`) outside your project.
   - **Protect production data.** Don't pull personal or sensitive field values into the AI session: names, SSNs, dates of birth, addresses, contact details, health, income, case notes, free-text comments or attachments. Prefer `SELECT COUNT()`, IDs and non-sensitive status fields. If record-level values are truly needed, use a sandbox with masked data, or get the user's explicit approval and follow the organization's data-handling policy (for example HIPAA, IRS Pub. 1075, SSA agreements or state data classification).
   - Never save production record data in the project, in git, or in logs the AI reads. Data snapshots go to the organization's approved storage.
   - These need the user's explicit GO for that specific action:
     - `sf project retrieve` from production into your project. A retrieve **overwrites local files**.
     - Any record change, including anonymous Apex, even when it is rolled back. (Mode B only. In Mode A, Claude never changes production records.)
   - Deploys depend on the mode:
     - **Mode A:** Claude never deploys to production. It prepares the PR and the release note and hands off.
     - **Mode B:** before a deploy, show three things: the exact components, the actual diff against production's **live** files, and the test level. Wait for GO. Run it as a direct command, not hidden inside a script.
3. **Verify live, every time.** Check in this order: live production, then the live sandbox, then local files. Never restate org state from memory or assume git matches the org. Re-verify even facts you established earlier in the session.
4. **Judge by live content, not branch history.** Teams often cherry-pick deploys. Decide whether something is "in prod" by comparing the live source, not by git ancestry.
5. **Never deploy a shared component from a stale base.** Some files are touched by many features, such as a navigation or tab container component. For those:
   - read the org's live source first, read-only through the Tooling API (rule 2), and commit it as a baseline;
   - apply additions only, and make sure the diff contains only the intended lines;
   - re-check that live hasn't changed just before the deploy (Mode B) or before the PR is approved (Mode A).
6. **Sandbox first.** Build, deploy and test in a sandbox. Production only gets components that were proven there.
7. **Additive by default.**
   - Prefer new components over editing existing flows, triggers, validation rules or classes.
   - "Hide, don't remove." Change only what was asked.
8. **Plan for every audience.**
   - Check internal users and Experience Cloud (portal) users separately, internal first after each deploy.
   - Never use `@salesforce/community/*` imports in components that also render internally.
9. **Map the impact before release.** List what an update will set off: triggers, record-triggered flows, validation rules, email alerts, scheduled jobs. Trace cascades: an update to object A whose trigger updates every related B fires B's automation too. Use `checklists/impact-analysis.md` and `checklists/cloud-specific-notes.md`.
10. **Stop on surprises.** If a deploy, test or permission check fails unexpectedly, stop, report the exact error and ask. Don't work around it.
11. **Have a rollback before every change.** Save `before/` copies of anything you overwrite, and write down how to undo each step. Note what can't be undone, such as enabling org features.

## Workflow

1. **Understand.** Read the request, then query the live orgs: schema, data counts, existing automation. Ask the business questions that change the design. Turn the answers into decisions and write them down.
2. **Design.** Start with an options table for each requirement: standard → declarative → Salesforce product → custom (rule 1). Write a short spec covering the data model, screens, rules, access, integrations, data migration, prod risks and rollback. Use these checklists:
   - `checklists/impact-analysis.md` and `checklists/cloud-specific-notes.md` for prod risks;
   - `checklists/integrations.md` for any connection to another system;
   - `checklists/data-migration.md` for any data load.

   Get the user's approval.
3. **Plan.** Break the work into small tasks with exact files, code and tests.
4. **Build in the sandbox.** One task at a time. Each task gets tests that assert real behaviour, a sandbox deploy, and a review (spec compliance + quality). Fix the findings before moving on.
5. **Test like a user.** Start with one small slice (one record or one parent record and its children), then run the real business path end to end through the UI, not just unit tests.
6. **Release gates** (see `checklists/prod-release.md`):
   - validation with targeted tests: through the pipeline in Mode A, or `sf project deploy validate` in Mode B;
   - a cascade pre-check where updates could cascade:
     - **Mode A:** in a production-like full-copy sandbox, or by the release manager in production;
     - **Mode B:** rolled back in production. It runs DML, so it needs a GO;
   - a data snapshot before any bulk user action;
   - **effective** access for the users who will run it, including Permission Set Groups;
   - the rollback written down.
7. **Release.**
   - **Mode A:** prepare the branch/PR with only the intended changes, and the CAB release note (`checklists/cab-release-note.md`). Stop. The pipeline or release manager deploys after approval.
   - **Mode B:** after the user's GO, quick-deploy exactly what was validated (`sf project deploy quick --job-id <id>`), one package at a time.
   - Either way, after the deploy: activate flows and do manual Setup steps from the checklist (each with an owner), then verify live.
8. **Record.** Update the release ledger: what's where, which commit or PR, which deploy ID.

## Commit hygiene
- Commit only when the user approves.
- Use the user's required author identity. Never change git config.
- Use scoped `git add` with the listed files only. Never commit secrets or org data.
- Watch for formatter hooks that rewrite files at commit. The committed file must match what was deployed and tested.

## Red flags — stop and re-check
| Thought | Reality |
|---|---|
| "Git has the latest version" | The org may differ. Read the live source first (read-only) |
| "It's just a small LWC change" | Shared components have wiped other features before |
| "It only updates one field" | Triggers and flows may cascade to hundreds of records and send emails |
| "Tests pass, so it works" | Tests set data directly. Run the real UI path too |
| "Prod is like the sandbox" | Check prod's live metadata; sandboxes drift |
| "I'll write a class for this" | Show the standard and declarative options first (rule 1) |
| "There's integration code in the sandbox, so deploy it" | Confirm the chosen approach first; it may be a leftover replaced by a standard feature (`checklists/integrations.md`) |
| "I'll query a few records to see the data" | Use counts, IDs and status fields. Personal data stays out of the AI session |
| "Just load the data" | Follow `checklists/data-migration.md`: dry run, matching keys, snapshot, reconciliation |
| "I'll fix it in prod quickly" | No prod action without explicit GO. In Mode A, no prod action by Claude at all |
| "There's no pipeline file, so I can deploy" | If unsure, ask. Default to Mode A |
