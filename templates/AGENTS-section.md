## Tickets (Notion): required on every code change

Engineering tickets live in Notion on [<Board name>](<board page URL>). Each ticket in the Tickets database belongs to a milestone; both databases sit on that page. Use the official Notion connector (claude.ai Settings > Connectors > Notion, or your tool's Notion integration).

- Tickets data source: `collection://<tickets id>`
- Milestones data source: `collection://<milestones id>`

Every change that reaches GitHub must be reflected on its ticket. This is part of the change, not a follow-up:

- **Before the first push of a branch:** find the ticket for the work. If there isn't one, create it under the right milestone (ask which milestone if it isn't clear) with Status "In progress".
- **When a PR is opened or marked ready for review:** set Status to "In review" and put the PR URL in Link.
- **When more commits are pushed to an open PR:** add a one-line note to the ticket saying what changed.
- **When a PR is merged:** set Status to "Done". If it is deployed and something still has to happen (a setting to change, a test to run), say so in a note or open a follow-up ticket.
- **When a PR is closed without merging:** set Status back to "Not started" or "Done" as appropriate, with a note saying why.
- **Non-code work** that changes production gets a ticket too.

Statuses are Not started, In progress, In review and Done. Work is not finished until its ticket matches what is on GitHub. If the Notion tools aren't available, say so to the person you're working with before you finish; don't skip the update silently.
