---
name: notion-ticket-sync
description: Keep a Notion ticket board in step with engineering work. Use when connecting Claude to Notion for the first time, when creating, updating or finding a ticket on the team's Notion board, when setting up ticket tracking in a repo, and on every git push or pull request change (open, ready, new commits, merge, close), since each one has to be reflected on its ticket.
---

# Notion ticket sync

Engineering tickets live in a Notion database. Each ticket belongs to one milestone (like an epic in Jira). This skill keeps that board matching what's on GitHub, so nobody has to update tickets by hand.

Anything Claude changes in Notion shows up under the name of the person whose Notion login is connected.

## 0. Your board (fill this in once)

Before first use, find these and keep them in the repo's `AGENTS.md` (see `templates/AGENTS-section.md`), so every session and every teammate's agent sees the same values:

- **Board page:** the Notion page that shows the milestone board.
- **Tickets data source:** `collection://...` (fetch the Tickets database in Notion to get it).
- **Milestones data source:** `collection://...`

If they aren't written down anywhere, search Notion for the board, fetch it, and ask the user to confirm before writing anything.

## 1. One-time setup (each person does this once)

You need to be a member of the Notion workspace. Then connect Notion in either place:

- **Claude app or claude.ai:** Settings, then Connectors, find Notion (the official one by Notion), Connect, log in and give it access to the workspace.
- **Claude Code in a terminal:** run this, then start a new session and use `/mcp` to log in to Notion:
  ```
  claude mcp add --transport http notion https://mcp.notion.com/mcp -s user
  ```

If both are connected, every Notion tool shows up twice. That's harmless; switch one off if it bothers you.

To check it works, ask: "list the milestones on our engineering board in Notion".

## 2. Board shape

The skill assumes two databases. If yours differs, fetch the data source and follow its actual property names and options; never guess them.

**Tickets**
- **Ticket** (title): a short, plain description of the work.
- **Status:** Not started, In progress, In review, Done.
- **Milestone:** a relation to one Milestones page. Query Milestones for its URL.
- **Priority:** Critical, High, Medium, Low.
- **Assignee:** a Notion user. Find people with the Notion user search, not by typing a name.
- **Link:** the PR URL once there is one.
- **Blocked by:** free text, for example "waiting on review" or "#42 merged".
- **Notes:** dated one-liners, newest last, for example "18 Nov: PR #42 opened for review."
- **Due date:** optional.

**Milestones:** Name, Status (Planned, In progress, Done), Owner, Target date. Order them by hand on the board page, with the current priority at the top.

## 3. The rule: every change that reaches GitHub is reflected on its ticket

This is part of the change, not a follow-up:

- **Before the first push of a branch:** find the ticket for the work. If there isn't one, create it under the right milestone with Status "In progress". Ask which milestone if it isn't clear.
- **When a PR is opened or marked ready for review:** set Status to "In review" and put the PR URL in Link.
- **When more commits are pushed to an open PR:** add a one-line note saying what changed.
- **When a PR is merged:** set Status to "Done". If something still has to happen after deploy (a setting to change, a test to run), say so in a note or open a follow-up ticket.
- **When a PR is closed without merging:** set Status back to "Not started" or "Done" as fits, with a note saying why.
- **Non-code work that changes production** (settings, config in third-party tools, a feature switched on) gets a ticket too.

Work isn't finished until its ticket matches what's on GitHub. If the Notion tools aren't available, say so to the person you're working with before you finish; don't skip the update silently.

## 4. Common jobs

- **Find a ticket:** query the Tickets data source (SQL mode) filtering on Ticket text, Link or Milestone, rather than a general Notion search.
- **Create a ticket:** create a page with the Tickets data source as parent, setting Ticket, Status, Milestone, Priority and Assignee. Put the why and the plan in the page body if it's more than a line.
- **Update a ticket:** change only the properties that changed. When adding to Notes, keep the existing notes and append the new dated line.
- **Before saying "done":** read the ticket back once to confirm the update stuck.

Things people can just ask:
- "add a ticket to the <milestone> milestone for <work>, assign it to me, high priority"
- "mark the #42 ticket as done"
- "what's still in review under <milestone>?"

## 5. Wiring it into a repo (optional, once per repo)

Two files make every agent in the repo follow the rule without being told:

1. **`AGENTS.md`:** paste `templates/AGENTS-section.md` and fill in your board's links. Add a `CLAUDE.md` containing just `@AGENTS.md` so Claude Code loads it too.
2. **`.claude/settings.json`:** merge in `templates/claude-settings-hook.json`. It's a PostToolUse hook that reminds Claude Code to update the ticket after `git push` and `gh pr create`, `ready`, `merge`, `close`, `reopen` or `edit`. Commit it so the whole team gets it.

The hook only reminds. Each person's Notion login (step 1) is what lets Claude actually make the update. The hook matches on command text, so a command that merely contains those words (for example a heredoc mentioning them) can trigger a harmless false reminder.
