# Production Release Checklist

Pick the mode first (see `SKILL.md` → Deployment modes). **If unsure, use Mode A.**
- **Mode A: pipeline / CAB.** Claude prepares the release and hands off. The pipeline or release manager deploys.
- **Mode B: direct CLI.** Claude deploys, with an explicit GO before each production step.

## 0. Gates (both modes, all must pass first)
- [ ] **Sandbox end to end:** passed, including the real UI path, not only unit tests.
- [ ] **Access check:** verify the **effective** access of the users who will run the feature, including permissions granted through Permission Set Groups. Code that queries `PermissionSetAssignment` for a permission set name misses group-granted access.
- [ ] **Cascade pre-check:** anonymous Apex: savepoint → update → catch and log errors → rollback. Records are rolled back and emails aren't sent.
  - **Mode A:** run it in a production-like full-copy sandbox, or have the release manager run it in production. Claude doesn't run DML in production.
  - **Mode B:** run it in production. It runs DML, so it needs a GO.
  - Caveat: Platform Events set to *Publish Immediately* are **not** rolled back.
  - Caveat: callouts can't run after uncommitted DML.
  - Fix bad data first.
- [ ] **Data snapshot:** export the records the release or the users will change.
- [ ] **Scheduled jobs:** by default, a scheduled Apex job blocks redeploying the classes it uses. Either turn on *Setup → Deployment Settings → Allow deployments of components when corresponding Apex jobs are pending or in progress*, or plan hotfixes as abort → deploy → reschedule.

## 1. Read production live (read-only, both modes)
- [ ] Confirm every new component is **absent** and every dependency is **present**.
- [ ] Note any existing triggers, flows and validation rules on the objects you touch.

## 2. Build the release package (both modes)
- [ ] Shared components come from production's **live** source, plus additions only.
- [ ] Save `before/` copies and write the rollback.
- [ ] Component list, diff against prod live, and the test classes to run.

---

## Mode A: pipeline / CAB hand-off
- [ ] **Branch and PR:** only the intended changes; the PR description links the release note.
- [ ] **Pipeline validation:** the pipeline validates against the higher environments (e.g. SIT → UAT → Staging) and runs the named tests. Every Apex class and trigger in the package must have ≥ 75% coverage from those tests.
- [ ] **CAB release note:** prepared from `checklists/cab-release-note.md`.
- [ ] **Stop.** Claude does not deploy. The pipeline or release manager deploys to production after CAB approval, in the agreed window.
- [ ] **After deploy:** Claude verifies live (read-only), lists the manual Setup steps and their owners, and updates the ledger with the PR and deploy ID.

## Mode B: direct CLI

### B1. Validate (after GO)
- [ ] `sf project deploy validate -o <prod> --source-dir ... --test-level RunSpecifiedTests --tests <your tests>`
- [ ] All tests pass, and **every Apex class and trigger in the package has ≥ 75% coverage from the tests you named**. With RunSpecifiedTests the org-wide figure doesn't count.

### B2. Deploy (after a second GO)
- [ ] `sf project deploy quick -o <prod> --job-id <validation id>` as a direct command. Production gets exactly what was validated, with no second test run. The validation must be recent (within 10 days) and nothing deployed since.
- [ ] Verify live: the components exist, and flows are active or activated by hand.

---

## 3. Manual Setup (both modes, each with an owner)
- [ ] Permission set assignments, group members, page layout buttons, reports, scheduled jobs.

## 4. Verify (both modes)
- [ ] Internal users first: the main screens still load.
- [ ] Portal users: unchanged unless intended.
- [ ] The feature works on one real record.

## 5. Record
- [ ] Ledger: commit or PR, deploy ID, date, what's pending.

## Rollback
- [ ] Deactivate the new flows and abort the new jobs.
- [ ] Redeploy the `before/` copies of the shared components. In Mode A, this goes through the pipeline as an emergency change.
- [ ] Remove buttons and access.
- [ ] Never delete data. New objects can stay unused.
