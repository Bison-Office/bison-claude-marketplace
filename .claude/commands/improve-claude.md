---
description: Troubleshoot or enhance Claude's behavior in a repository
argument-hint: [describe desired behavior or capability]
---

Help user improve Claude's behavior. User input: $ARGUMENTS

First, fetch and review all relevant documentation:
- https://code.claude.com/docs/en/skills
- https://code.claude.com/docs/en/slash-commands
- https://code.claude.com/docs/en/plugins
- https://code.claude.com/docs/en/sub-agents
- https://code.claude.com/docs/en/hooks-guide
- https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices

Then follow this diagnostic flow:

1. **Gather requirements** - Ask user to describe:
   - What behavior they want Claude to have
   - What Claude is currently doing wrong (or not doing)
   - Which repository they want this behavior in

2. **Check existing marketplace** - Review `.claude-plugin/marketplace.json` to see if any plugin already provides the described capability or knowledge. Check skills, commands, agents within each plugin.

3. **If matching plugin exists:**
   - Ask: "Have you installed the `<plugin-name>` plugin in your target repository?"
   - If yes: Suggest updating marketplace via Claude (`/plugins → marketplaces → bison_tools → update_marketplace`), then uninstall and reinstall the plugin in their target repo
   - Ask user to test again after reinstall

4. **If reinstall didn't help:**
   - Acknowledge the plugin's description or content may need improvement
   - Read the relevant skill/command/agent file
   - Identify what's missing or unclear
   - Suggest specific improvements to make Claude follow the intended behavior

5. **If no matching plugin/component exists:**
   - Confirm this is a gap in the marketplace
   - Suggest either:
     - Creating a new component in an existing plugin (offer `/create-skill`, `/create-command`, `/create-agent`)
     - Creating a new plugin (offer `/create-plugin`)
   - Challenge whether it should be a skill, command, agent, or hook based on the use case
