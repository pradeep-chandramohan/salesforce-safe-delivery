# Example (anonymized): annual contractor rate renewal

*All names, numbers, objects and orgs are fictional. The domain is simplified.*

*This team used **Mode B (direct CLI)**: no pipeline, and every production step after an explicit GO. In a Mode A (pipeline/CAB) team, the same work ends with a PR and a CAB release note instead of the deploy and the production pre-check.*

**Request:** about 40 contracts reach their anniversary next month. Each contractor, or vacant seat, needs the new year's hourly rate, and the history must show which rate applied on which dates.

**How the skill shaped the delivery:**

| Step | What happened |
|---|---|
| Understand | Queried production live: 40 contracts, 300 contractors, 5 vacant seats awaiting replacements. Found that production had no rate history at all |
| Business questions | Is a rate per person or per seat? (Per seat: a replacement gets the seat's rate for the year they join.) When should the switch happen? (On the anniversary date.) Must every person have a rate before renewal? (Yes, so renewal blocks) |
| Standard first | Reports and related lists for viewing rates (no custom screen). Custom only where needed: Flow can't parse an uploaded Excel file, so a small upload component was built |
| Design | A private Rate Schedule object (one record per seat × year, with a unique key and period dates); a one-time Excel upload with a "current rate vs file" check and per-row decisions; renewal behind an existing button; a daily reminder |
| Data migration | The business filled a **pre-filled template** exported from Salesforce, so the unique IDs were already correct. A preview step and a snapshot came before save, and re-uploads update rather than duplicate |
| Impact analysis | Renewal updates contract dates, and an existing trigger copies them to **every** contractor on the contract. That sets off validation rules and a notification flow with no "only when changed" filter. Mitigation: a rolled-back pre-check on production data (with a GO), and tidying two risky records |
| Shared component | The navigation component's live source was read, committed as a baseline, and changed by +21 lines only |
| Testing | One contract first, then the real path: a contractor leaves, a replacement is requested, a candidate is selected and awarded. The replacement inherited the seat's current-year rate |
| Release | Gates, validate, quick deploy after GO, manual Setup, verification, then the ledger |

**Caught before production:**
- a Text → Number field change Salesforce refused;
- a copied button that showed the wrong label;
- a formatter hook that rewrote a class at commit;
- a partial-save risk in a trigger;
- duplicate reminder emails on rerun.
