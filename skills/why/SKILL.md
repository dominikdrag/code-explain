---
name: why
description: This skill should be used when the user asks "why does this code exist", "why is this line here", "what's the history of this", "who wrote this and why", or uses "/why". Provides quick historical context for specific lines of code.
---

# Why Skill

Quick explanation of why specific code exists, using git history and contextual analysis.

## Invocation

```
/why <file:line>
/why <file:start-end>
```

**Parameters:**
- `file:line` - File path and line number (required)
- `file:start-end` - File path with line range

**Examples:**
```
/why src/utils/retry.ts:42
/why src/auth/oauth.ts:87-95
/why config/settings.py:120
```

## Workflow

### Step 1: Parse Target

Extract:
1. File path
2. Line number or range

Validate the file exists and line numbers are valid.

### Step 2: Read Context

Read the target file and extract:
1. The specific line(s) in question
2. Surrounding context (5-10 lines before/after)
3. The function/class containing this code

### Step 3: Launch History Detective

Use the Task tool to launch the `history-detective` agent with focused scope:

```
Prompt for history-detective:
Analyze the git history for: [file]
Focus on lines: [line or range]

Find:
1. The commit that introduced these lines
2. The commit message explaining why
3. Any linked issues or PRs
4. The author and date
5. Any subsequent modifications
```

### Step 4: Synthesize Quick Answer

Combine the history data with code context to provide a concise explanation:

```markdown
## Why: [file:line]

### The Code
```[language]
[the specific lines]
```

### Historical Context
- **Introduced:** [date] by [author]
- **Commit:** [sha] - "[message]"
- **Related:** [issue/PR if any]

### Explanation
[1-2 paragraph explanation of why this code exists based on:
- The commit message
- The surrounding code
- Any linked issues
- The author's apparent intent]

### Related Changes
[List any significant modifications since introduction]
```

## Quick Response Format

For simple queries, provide a concise response:

```markdown
**Line 42 of retry.ts**

Added by @alice on 2024-03-15 in commit `abc1234`:
> "Add exponential backoff to prevent thundering herd"

This 10-second max delay was added after incident #456 where
simultaneous retries overwhelmed the payment API.
```

## When to Expand

Provide more detail when:
- The commit message is vague ("fix bug", "update")
- Multiple commits modified the same lines
- The code appears to be a workaround
- There's linked issue/PR with discussion

## Error Handling

- **File not found:** Suggest similar files
- **Line out of range:** Show file length, suggest valid range
- **No git history:** Note file may be new or git history unavailable
- **Unhelpful commits:** Note that history doesn't explain intent, analyze code structure instead

## Tips for Better Results

If git history is unhelpful:
1. Look at surrounding code comments
2. Check if there are tests that explain behavior
3. Look for related documentation
4. Note that this may be legacy code without documented rationale
