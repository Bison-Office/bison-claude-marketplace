---
description: Create a Linear story from investigation findings
argument-hint: [optional: issue description]
allowed-tools: Bash(git:*), Read, Glob, Grep
---

Create a Linear story for: $ARGUMENTS

## Context
- Current branch: !`git branch --show-current`
- Recent git activity: !`git log --oneline -5 2>/dev/null || echo "No recent commits"`

## Instructions

Use the `development-workflow` skill for story creation guidelines.

## Story Template

When creating the story, include:

### Title
Clear, actionable title describing the work

### Description
- **Problem/Goal**: What needs to be solved or achieved
- **Context**: Background information and investigation findings
- **Acceptance Criteria**: Clear definition of done
- **Technical Notes**: Implementation hints or constraints (if applicable)

### Labels
Apply appropriate labels based on story type:
- `bug` - for defects
- `feature` - for new functionality
- `improvement` - for enhancements
- `tech-debt` - for technical improvements

## After Creation
- Note the story ID (BIS-XXXX)
- Consider using `/implement-story BIS-XXXX` to start implementation
