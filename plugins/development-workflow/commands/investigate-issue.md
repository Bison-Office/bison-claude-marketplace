---
description: Investigate an issue and document findings
argument-hint: [describe the issue or feature]
allowed-tools: Bash(git:*), Bash(dotnet:*), Bash(npm:*), Read, Glob, Grep
---

Investigate this issue: $ARGUMENTS

## Context
- Current branch: !`git branch --show-current`
- Recent changes: !`git log --oneline -5 2>/dev/null || echo "No recent commits"`

## Skill Selection

Choose the appropriate skill based on issue type:

| Issue Type | Skill | When to Use |
|------------|-------|-------------|
| Production problems | `production-troubleshooter` | "Why is X not working?" questions |
| System health | `diagnostics` | General system health checks |
| Data queries | `sql-queries` | Database investigation |
| Query performance | `database-patterns` | Slow queries, optimization |
| Data relationships | `entity-models` | Understanding data models |
| Pricing behavior | `pricing-algorithm` | Pricing/buy box questions |
| Amazon API | `amazon-sp-api` | Amazon SP-API issues |

## Investigation Process

1. **Understand the Problem**
   - What is the expected behavior?
   - What is the actual behavior?
   - When did it start? Any recent changes?

2. **Gather Evidence**
   - Check logs and error messages
   - Query relevant data
   - Review recent code changes

3. **Identify Root Cause**
   - Trace the issue through the system
   - Isolate the component or logic causing the problem

4. **Document Findings**

## Output Format

```markdown
## Investigation: [Issue Title]

### Summary
Brief description of the issue and findings.

### Root Cause
What is causing the issue and why.

### Evidence
- Logs, queries, or code references supporting the conclusion
- File paths and line numbers where relevant

### Recommended Next Steps
1. [Specific action item]
2. [Specific action item]

### Additional Notes
Any context, caveats, or related issues discovered.
```

## After Investigation

If a fix is needed, consider:
- `/create-story` - Create a Linear story for the fix
- `/implement-story BIS-XXXX` - Start implementing the fix
