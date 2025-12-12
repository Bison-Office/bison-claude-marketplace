---
description: Create a new slash command in the marketplace
argument-hint: [optional command description]
---

Create a new slash command. User input: $ARGUMENTS

First, fetch and review:
- https://code.claude.com/docs/en/slash-commands

Then interactively:
1. Ask what the command should do and what arguments it needs
2. Challenge if this should be a command vs skill/agent
3. List existing plugins (`plugins/*/plugin.json`) and suggest the best fit, or offer `/create-plugin`
4. Confirm name, location, description, argument-hint before creating
5. Create the command file with proper frontmatter
