---
description: Create a new skill in the marketplace
argument-hint: [optional skill description]
---

Create a new skill. User input: $ARGUMENTS

First, fetch and review:
- https://code.claude.com/docs/en/skills
- https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices

Then interactively:
1. Ask what knowledge/data the skill should contain
2. Challenge if this should be a skill vs command/agent
3. List existing plugins (`plugins/*/plugin.json`) and suggest the best fit, or offer `/create-plugin`
4. Confirm name, location, description before creating
5. Create the skill file with proper frontmatter
