---
description: Create a new plugin in the marketplace
argument-hint: [optional plugin name or domain]
---

Create a new plugin. User input: $ARGUMENTS

First, fetch and review:
- https://code.claude.com/docs/en/plugins

Then interactively:
1. Ask what domain this plugin covers and what components it will contain
2. Check existing plugins (`plugins/*/plugin.json`) to avoid conflicts
3. Confirm name and initial structure before creating
4. Create `plugins/<name>/plugin.json` and subdirectories
5. Offer to create first skill/command/agent via respective `/create-*` commands
