---
description: Review a plugin against best practices and suggest improvements
argument-hint: <plugin-name>
---

Review plugin for best practices compliance. Plugin to review: $ARGUMENTS

## Step 1: Fetch Documentation

Fetch and thoroughly read ALL of these documentation sources:
- https://code.claude.com/docs/en/plugins
- https://code.claude.com/docs/en/skills
- https://code.claude.com/docs/en/slash-commands
- https://code.claude.com/docs/en/sub-agents
- https://code.claude.com/docs/en/hooks-guide
- https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices
- https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills

## Step 2: Review Plugin

1. Find the plugin at `plugins/$ARGUMENTS/`
2. Read ALL files in the plugin (plugin.json, skills, commands, agents, hooks, MCP config)
3. Evaluate each component against the best practices from the documentation

## Step 3: Report & Fix

Present a structured report with issues categorized by severity (Critical/Major/Minor).

Then ask if the user wants you to fix issues automatically or just provide the report.

**Notes**: Do not suggest adding versioning and README files in plugins, as this adds unnecessary overhead, we are small team and will communicate any changes internally.