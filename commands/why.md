---
description: Quick explanation of why specific code exists using git history and contextual analysis
argument-hint: file:line or file:start-end (e.g., src/auth.ts:42 or src/auth.ts:40-50)
---

# Why Workflow

You are helping a developer understand why specific code exists by analyzing git history and context.

## Core Principles

- **Focus on the "why"**: Commit messages and history reveal intent
- **Be concise**: This is a quick lookup, not a full explanation
- **Provide actionable context**: Who to ask, what issues to reference

---

## Phase 1: Parse Target

**Goal**: Identify the specific code to explain

Initial request: $ARGUMENTS

**Actions**:
1. Parse the request:
   - **File path**: Required
   - **Line number or range**: Required (e.g., `:42` or `:40-50`)

2. Validate:
   - File exists
   - Line numbers are within file range

3. If no arguments, ask user: "Which file and line would you like me to explain?"

---

## Phase 2: Read Context

**Goal**: Get the code and surrounding context

**Actions**:
1. Read the target file
2. Extract:
   - The specific line(s) in question
   - 5-10 lines of surrounding context
   - The function/class containing this code

---

## Phase 3: Git History Analysis

**Goal**: Find out when and why this code was written

**Actions**:
1. Launch **history-detective agent** with focused scope:

```
Analyze git history for: [file]
Focus on lines: [line or range]

Find:
1. The commit that introduced these specific lines
2. The commit message explaining why
3. Any linked issues or PRs (look for #123, JIRA-456, etc.)
4. The author and date
5. Any subsequent modifications to these lines
```

2. Wait for agent to return JSON with:
   - blame data
   - key commits
   - linked issues
   - insights

---

## Phase 4: Synthesize Answer

**Goal**: Provide a concise explanation

**Actions**:
1. Format the response:

**For simple queries (clear commit message):**
```markdown
**Line 42 of src/utils/retry.ts**

Added by @alice on 2024-03-15 in commit `abc1234`:
> "Add exponential backoff to prevent thundering herd"

This 10-second max delay was added after incident #456 where
simultaneous retries overwhelmed the payment API.
```

**For complex queries (vague history or multiple changes):**
```markdown
## Why: src/auth/oauth.ts:87-95

### The Code
```typescript
[the specific lines]
```

### Historical Context
- **Introduced:** 2024-03-15 by @alice
- **Commit:** abc1234 - "Add OAuth support"
- **Related:** Issue #123 - "Add Google login"

### Explanation
[1-2 paragraph explanation based on:
- The commit message
- The surrounding code
- Any linked issues
- The author's apparent intent]

### Related Changes
- 2024-06-01: Modified by @bob - "Fix token refresh race condition"
- 2024-08-15: Modified by @alice - "Add GitHub provider support"
```

---

## When to Expand

Provide more detail when:
- The commit message is vague ("fix bug", "update")
- Multiple commits modified the same lines
- The code appears to be a workaround
- There's a linked issue/PR with discussion

---

## Error Handling

- **File not found**: Suggest similar file names
- **Line out of range**: Show file length, suggest valid range
- **No git history**: Note file may be new or not tracked
- **Unhelpful commits**: Analyze code structure instead, note that history doesn't explain intent
