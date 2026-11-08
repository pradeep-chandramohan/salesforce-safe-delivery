# Data Migration Checklist (legacy app / spreadsheets → Salesforce)

## Plan
- [ ] **Standard tools first:** Data Import Wizard, Data Loader, or the Bulk API. A custom load screen only when users must run the load themselves with checks; say why.
- [ ] **Source of truth:** who owns the data, and who signs off that it's correct?
- [ ] **Field mapping:** source → target, with transformations, defaults and picklist value mapping. Mark fields that are **not** migrated, and why.
- [ ] **Matching key:** match on a stable unique ID (an external ID or an auto-number), **never on names**. Where possible, give the business a **pre-filled template** exported from Salesforce, so the IDs are already correct.
- [ ] **Old application fields:** leave legacy fields and objects untouched; don't repurpose them. Document which fields the new process uses.
- [ ] **Automation:** list the triggers, flows and validation rules that will fire on insert or update (see `impact-analysis.md`). Decide whether emails or tasks should fire during the load.
- [ ] **Ownership, sharing and audit:** who will own the records, and who can see them?

## Rehearse in a sandbox
- [ ] **Dry run with a full-size file.** Measure the errors, the time and the governor limits.
- [ ] **Preview before saving:** show matched, unmatched, duplicate and changed rows, and differences from current values. A human decides each difference.
- [ ] **Re-runs:** an upsert on a unique key means re-loading the same file changes nothing and never duplicates.

## Load in production
- [ ] **Snapshot** the records that will change, as an export.
- [ ] **Load in one transaction per batch** (all or nothing), with an upload log record and the source file attached.
- [ ] **Reconcile:** compare counts and totals between source and Salesforce. Keep a "rows not loaded" file with reasons.
- [ ] **Sign-off** from the data owner.

## Rollback
- [ ] Restore from the snapshot. Delete or deactivate loaded records only with explicit approval. Never delete data you can't restore.
