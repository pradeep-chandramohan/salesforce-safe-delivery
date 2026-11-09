# Salesforce safe delivery — paste into your CLAUDE.md

```markdown
## Salesforce safe delivery rules
- Deployment mode: we use [Mode A: pipeline/CAB — you prepare the PR + CAB release note and NEVER deploy or run DML in production | Mode B: direct CLI — you may validate and quick-deploy after my GO]. If unsure, assume Mode A.
- Standard first: for every requirement show options in order (standard config → Flow/declarative → Salesforce products e.g. Einstein Activity Capture/AppExchange → custom Apex/LWC) with trade-offs, and let me choose. Custom only with a one-line reason. Never deploy leftover custom integration code without confirming it's the chosen approach.
- Data migration: match on stable unique IDs (never names), pre-filled templates from Salesforce, preview + decisions before save, snapshot first, reconcile counts after.
- Production is READ-ONLY: SOQL/Tooling queries and read-only source reads into a separate folder are fine. `sf project retrieve` into the project needs my GO (it overwrites local files). Mode A: never deploy or change records in production. Mode B: deploys and any record change, including rolled-back anonymous Apex, need my explicit GO for that specific action; before a deploy show components, diff vs prod's LIVE files, test level, then wait.
- Protect production data: from production read only metadata, counts, IDs and non-sensitive status fields. Never pull personal or sensitive values (names, SSNs, DOB, addresses, health, income, case notes, attachments) into the session, project, git or logs. If record values are needed, use a masked sandbox or get my explicit approval under our data-handling policy.
- Verify LIVE every time, in this order: production → sandbox → local files. Never assume git matches the org; re-check facts even from earlier in the session.
- Teams cherry-pick deploys: decide "is it in prod?" by comparing live source, not git history.
- Shared components (e.g. navigation/tab containers): read the org's live source first (read-only), commit it as a baseline, apply ADDITIONS ONLY, re-check live right before deploying.
- Sandbox first. Prefer additive changes: new components over editing existing flows/triggers/validation rules/classes. Hide, don't remove.
- Plan for both internal users and Experience Cloud (portal) users; check internal first after each deploy. No @salesforce/community imports in components that render internally.
- Before release, list what the change triggers (triggers, record-triggered flows, validation rules, email alerts, scheduled jobs) and trace cascades.
- Release gates: validation with targeted tests (every class/trigger ≥75% from those tests); cascade pre-check where updates cascade (Mode A: full-copy sandbox or the release manager; Mode B: rolled back in prod after my GO; Publish-Immediately platform events aren't rolled back); a data snapshot; a written rollback. Mode A: prepare the PR + CAB release note and stop. Mode B: `sf project deploy validate`, then `sf project deploy quick` of that validation after my GO.
- If anything fails unexpectedly: stop, report the exact error, ask. Don't work around it.
- Commit only when asked, with my required author identity; scoped `git add`; never commit secrets or org data.
```
