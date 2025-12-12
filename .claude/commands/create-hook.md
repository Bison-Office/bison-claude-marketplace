---
description: Create a new hook in Claude Code settings
argument-hint: [optional hook type or description]
---

Create a new hook. User input: $ARGUMENTS

First, fetch and review:
- https://code.claude.com/docs/en/hooks-guide

Then interactively:
1. Ask what the hook should do and when it should trigger
2. Ask if project-specific (`.claude/settings.json`) or user-wide (`~/.claude/settings.json`)
3. Review existing hooks in settings to avoid conflicts
4. Confirm event type, matcher, command before creating
5. Add hook to appropriate settings.json
