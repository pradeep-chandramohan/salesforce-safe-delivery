# CAB Release Note — template

*Claude fills this in for every Mode A release. Keep it factual: every claim should point to evidence such as a test run, a query result or a diff.*

---

## 1. Change summary
- **Title:**
- **Business reason / requested by:**
- **Users affected:** internal / portal / both, and roughly how many
- **Requested deployment window:**

## 2. Components
| Type | API name | New / Changed | Notes |
|---|---|---|---|
| | | | |

**Diff against production's live source:** link or attach. For shared components, confirm it's **additions only**.

## 3. Impact analysis
| Object | Automation that fires | Condition | Risk | Mitigation |
|---|---|---|---|---|
| | | | | |

*(From `checklists/impact-analysis.md` and `checklists/cloud-specific-notes.md`.)*

## 4. Testing evidence
- **Sandbox(es) and dates:**
- **Test classes to run in production (RunSpecifiedTests):**
- **Results:** pass/fail counts, plus coverage per class and trigger (each ≥ 75%)
- **End-to-end UI test:** scenario and result
- **Cascade pre-check:** where it was run (full-copy sandbox or by the release manager in prod), and the result

## 5. Data
- **Data changes during or after release:** none / describe
- **Snapshot taken:** location and time

## 6. Rollback plan
- **Steps to undo:** each with an owner.
- **What can't be undone:** e.g. org features enabled, emails already sent.
- **Rollback decision point:** who decides, and by when.

## 7. Post-deployment manual steps
| Step | Owner | Done |
|---|---|---|
| | | |

## 8. Verification after deploy
- Internal screens load.
- Portal unchanged (or changed as intended).
- One real record works.

## 9. Approvals
| Role | Name | Date |
|---|---|---|
| Technical reviewer (PR) | | |
| Business owner | | |
| CAB | | |
