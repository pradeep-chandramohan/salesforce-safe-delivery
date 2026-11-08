# Integrations Checklist

## Choose the approach (standard first)
- [ ] **Is there a Salesforce-provided feature?** E.g. Einstein Activity Capture for email and calendar sync with Google or Microsoft, Salesforce Connect / External Objects, Experience Cloud for partner access, standard connectors or MuleSoft, or an AppExchange package.
- [ ] **Licensing:** which licences or permission set licences are needed, how many users, and the cost? Check what's actually assigned in production versus the sandbox.
- [ ] **Custom integration (Named Credentials + Apex callouts, Platform Events) only if the above can't meet the need.** Write down why.

## Security
- [ ] Use **Named Credentials / External Credentials**. No secrets in code, custom settings or git.
- [ ] Least-privilege integration user and scopes. Review the connected app policies.
- [ ] Decide what data leaves the org, and whether that's allowed (privacy and compliance).

## Design
- [ ] **Direction and timing:** inbound, outbound or both; real-time or batch; who is the source of truth?
- [ ] **Errors and retries:** log failures, alert someone, and stop duplicates on retry (idempotency keys).
- [ ] **Limits:** API call limits, callout limits, and timeouts at production volume.
- [ ] **Users who sync:** e.g. Activity Capture needs the right users enabled. Check every role that depends on the sync, both the people creating events and the people whose calendars are read.

## Release
- [ ] Sandboxes may hold **leftover or experimental integration code** (triggers, classes, remote sites). Never deploy it without confirming it's the chosen approach. Check whether those triggers are still active in the sandbox.
- [ ] Production setup steps (credentials, user enablement, licence assignment) go on a manual checklist, with an owner.
- [ ] Test end to end with real accounts in a sandbox before production.
