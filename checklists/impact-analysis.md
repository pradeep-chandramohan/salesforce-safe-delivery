# Impact Analysis (before any release that updates records)

For each object your change writes to:

1. **Triggers:** `SELECT Name, Status FROM ApexTrigger WHERE TableEnumOrId = '<Object>'` (Tooling API). Read what each one does after update. Does it update *other* objects?
2. **Record-triggered flows:** list active flows whose start object is yours. Check their entry criteria:
   - Is there an "only when changed" condition?
   - A flow with no change filter runs on **every** update and may resend emails.
3. **Validation rules:** list the active rules. Could the cascade fail on old or bad records? One bad record can roll back a whole transaction.
4. **Cascades:** if A's trigger updates every related B, repeat steps 1-3 for B.
5. **Emails and tasks:** which email alerts, notifications or tasks could fire? Who receives them?
6. **Scheduled jobs and batches:** do any read the fields you change?
7. **Limits:** estimate the SOQL, DML rows and email invocations at production scale.

8. **Cloud-specific automation:** see [`cloud-specific-notes.md`](cloud-specific-notes.md) for Service, Sales and Experience Cloud and custom app items.

Write the result as a table with columns: object → automation → condition → risk → mitigation. Share it before release.
