---
name: trace
description: This skill should be used when the user asks to "trace this function", "follow the execution", "what calls this", "trace the flow", "how does data flow through", or uses "/trace". Traces execution flow through the codebase for a function or method.
---

# Execution Trace Skill

Trace the execution flow of a function or method through the codebase, mapping entry points, transformations, and exit points.

## Invocation

```
/trace <function_name> [--max-depth=N]
```

**Parameters:**
- `function_name` - Name of function or method to trace (required)
- `--max-depth` - Maximum call depth to trace (default: 10)

**Examples:**
```
/trace handleCheckout
/trace processPayment --max-depth=5
/trace UserService.authenticate
```

## Workflow

### Step 1: Locate Function

Use Grep and Glob to find the function definition:

```
Search patterns:
- "function <name>"
- "const <name> ="
- "def <name>"
- "func <name>"
- "<class>.<name>"
```

If multiple matches found, present options to user for clarification.

### Step 2: Find Entry Points

Identify how this function gets called:

1. **Direct callers** - Functions that call this directly
2. **Route handlers** - If web app, which routes invoke this
3. **Event handlers** - Event listeners that trigger this
4. **Scheduled jobs** - Cron or scheduled invocations
5. **CLI commands** - Command-line entry points

Use Grep with patterns like:
- `<function_name>(`
- `await <function_name>`
- `<function_name>.call`

### Step 3: Trace Forward

From the function, trace what it calls:

1. Read the function implementation
2. Identify all function calls within
3. For each called function, recursively trace (up to max-depth)
4. Track:
   - Data transformations
   - Side effects (DB writes, API calls, file I/O)
   - Error handling paths
   - Return values

### Step 4: Build Flow Diagram

Create an ASCII sequence diagram showing:

```
┌─────────┐     ┌─────────────┐     ┌───────────┐
│  Caller │────▶│  Target Fn  │────▶│  Service  │
└─────────┘     └─────────────┘     └───────────┘
                      │
                      ▼
               ┌─────────────┐
               │  Database   │
               └─────────────┘
```

### Step 5: Document Each Step

For each step in the trace, document:

```markdown
### Step N: [Function Name] (file:line)

**Input:** [Parameters and their types]
**Processing:** [What happens]
**Output:** [Return value]
**Side Effects:** [DB writes, API calls, etc.]
**Error Paths:** [What can go wrong]
```

## Output Format

```markdown
# Execution Trace: [function_name]

## Entry Points
- [Route/handler 1] → calls [function]
- [Route/handler 2] → calls [function]

## Flow Diagram
[ASCII diagram]

## Detailed Trace

### Step 1: [Entry Point]
[Description]

### Step 2: [Target Function]
[Description with line references]

### Step 3: [Called Function]
[Description]

...

## Data Transformations
- Input: [original shape]
- After Step 2: [transformed shape]
- Output: [final shape]

## Side Effects
- Step 2: Writes to `users` table
- Step 4: Sends HTTP request to payment API

## Error Handling
- Step 2: Throws `ValidationError` if input invalid
- Step 4: Retries 3x on network failure

## Performance Considerations
- Step 3 has O(n) complexity
- Step 4 makes external API call (potential bottleneck)
```

## Depth Limiting

When `--max-depth` is reached:
- Note that trace continues but was not followed
- Mention the function that would be traced next
- Suggest increasing depth if user needs more detail

## Error Handling

- **Function not found:** Search for similar names, suggest alternatives
- **Ambiguous name:** Present all matches, ask user to specify file
- **Recursive calls:** Detect and note cycles, don't infinite loop
- **External libraries:** Note when trace enters node_modules or external code
