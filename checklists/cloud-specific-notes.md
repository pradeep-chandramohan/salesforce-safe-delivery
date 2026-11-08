# Cloud-specific notes (add to the impact analysis)

Each Salesforce cloud has automation that's easy to miss. When your change touches these areas, check them too.

## Service Cloud
- **Case assignment and escalation rules:** a field update can re-route or escalate cases.
- **Auto-response rules / Email-to-Case / Web-to-Case:** can a change resend customer emails?
- **Entitlements and Milestones:** status or field changes can start, pause or violate SLA timers.
- **Omni-Channel routing:** changes to queues, skills or case fields can re-route work.
- **Macros and quick actions** that set fields your change relies on.
- **Knowledge:** article visibility and channels when you change data categories or permissions.

## Sales Cloud
- **Lead assignment rules** and lead conversion field mapping.
- **Opportunity stages, forecasting** and stage-based automation.
- **Territory rules** and account or opportunity sharing.
- **Approval processes** locked by record or status changes.
- **CPQ, if present:** price rules and quote calculations that fire on updates.

## Experience Cloud (portals)
- **External sharing defaults:** they should usually be Private. Test that one portal user can't see another account's data.
- **Guest user access:** check it on every page and Apex class.
- **`@salesforce/community/*` imports:** never in components that also render internally.
- **Builder changes (headers, Head Markup, pages) aren't in git:** keep a manual checklist and publish the site after changes.
- **Apex class access for portal profiles:** "The Apex request is invalid" usually means a missing class grant.

## Platform / custom apps
- **Triggers that copy fields to child records:** one parent update can cascade to many children and their automation.
- **Record-triggered flows without an "only when changed" condition:** they run on every update and may resend emails.
- **Scheduled Apex/batches:** while scheduled they block redeploying their classes.
- **Shared navigation or tab components:** build on the live source, additions only.
- **Permission sets vs Permission Set Groups:** code that checks direct assignments misses group-granted access.
