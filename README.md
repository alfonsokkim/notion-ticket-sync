# notion-ticket-sync

A Claude skill that keeps a Notion ticket board in step with your GitHub work. Once it's installed, Claude creates and updates tickets for you as you push branches and open, update, merge or close pull requests, so nobody has to move tickets by hand.

## What's in here

- `SKILL.md`: the skill itself. It covers connecting Notion, the board's shape (tickets grouped under milestones), the update rule, and everyday jobs like finding or creating a ticket.
- `templates/AGENTS-section.md`: a section to paste into a repo's `AGENTS.md`, so every coding agent working in that repo follows the rule. Fill in your board's link and database IDs.
- `templates/claude-settings-hook.json`: a Claude Code hook for the repo's `.claude/settings.json`. It reminds Claude to update the ticket after `git push` and `gh pr` commands.

## Install

**Claude Code:**

```
git clone https://github.com/alfonsokkim/notion-ticket-sync ~/.claude/skills/notion-ticket-sync
```

**Claude app or claude.ai:** download this repo as a zip (Code, then Download ZIP) and upload it under Settings, then Capabilities, then Skills.

## Set up Notion (once per person)

- **Claude app or claude.ai:** Settings, then Connectors, find Notion, Connect, and give it access to your workspace.
- **Claude Code:** run the command below, then start a new session and use `/mcp` to log in.

  ```
  claude mcp add --transport http notion https://mcp.notion.com/mcp -s user
  ```

Then ask Claude "list the milestones on our engineering board in Notion" to check it works. The first time, it will help you find your board and note its IDs.

## Optional: wire it into a repo

Paste `templates/AGENTS-section.md` into the repo's `AGENTS.md` (and add a `CLAUDE.md` containing `@AGENTS.md`), then merge `templates/claude-settings-hook.json` into `.claude/settings.json` and commit both. Everyone working in that repo then gets the same rule and reminder.
