---
description: Create a new agent definition in the marketplace
argument-hint: [optional agent description]
---

Create a new agent. User input: $ARGUMENTS

First, fetch and review:
- https://code.claude.com/docs/en/sub-agents

Then interactively:
1. Ask what task the agent should handle autonomously and what tools it needs
2. Challenge if this should be an agent vs skill/command
3. List existing plugins (`plugins/*/plugin.json`) and suggest the best fit, or offer `/create-plugin`
4. Confirm name, location, description, tools before creating
5. Create the agent file with proper frontmatter
