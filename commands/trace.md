---
description: Trace execution flow of a function through the codebase, mapping entry points, data transformations, and side effects
argument-hint: Function or method name [--max-depth=N]
---

# Execution Trace Workflow

You are helping a developer understand how a function executes by tracing its flow through the codebase.

## Core Principles

- **Trace systematically**: Follow execution from entry to exit
- **Document side effects**: Note all external interactions (DB, API, file I/O)
- **Show data transformations**: Track how data changes through the flow
- **Use TodoWrite**: Track progress throughout

---

## Phase 1: Locate Function

**Goal**: Find the function definition

Initial request: $ARGUMENTS

**Actions**:
1. Create todo list with phases
2. Parse the request:
   - **Function name**: Required
   - **Max depth**: Default 10
3. Use Grep to find the function definition:
   - `function <name>`
   - `const <name> =`
   - `def <name>`
   - `func <name>`
   - `<Class>.<method>`

4. If multiple matches found, present options and ask user to specify
5. Read the function implementation

---

## Phase 2: Find Entry Points

**Goal**: Identify how this function gets called

**Actions**:
1. Search for all callers using Grep:
   - `<function_name>(`
   - `await <function_name>`
   - `<function_name>.call`
   - `<function_name>.apply`

2. Categorize entry points:
   - **Direct callers**: Functions that call this directly
   - **Route handlers**: HTTP routes that invoke this
   - **Event handlers**: Event listeners that trigger this
   - **Scheduled jobs**: Cron or scheduled invocations
   - **CLI commands**: Command-line entry points

3. Document each entry point with file:line references

---

## Phase 3: Trace Forward

**Goal**: Map what the function calls and its side effects

**Actions**:
1. For the target function, identify:
   - All function calls within
   - External service calls (APIs, databases)
   - File system operations
   - State mutations

2. Recursively trace called functions (up to max-depth):
   - Read each called function
   - Note its purpose and side effects
   - Track data transformations

3. Build the call tree with:
   - Function name and location
   - Input parameters
   - Return values
   - Side effects

**Stop tracing when**:
- Max depth reached
- Entering external libraries (node_modules, etc.)
- Recursive call detected (note the cycle)

---

## Phase 4: Build Flow Diagram

**Goal**: Create visual representation of the flow

**Actions**:
1. Create ASCII sequence diagram:

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

2. Include all significant actors:
   - Entry points
   - Target function
   - Called services
   - External systems

---

## Phase 5: Document the Trace

**Goal**: Present comprehensive trace documentation

**Actions**:
1. Format the output:

```markdown
# Execution Trace: [function_name]

## Entry Points
- [Route/handler 1] → calls [function]
- [Route/handler 2] → calls [function]

## Flow Diagram
[ASCII diagram]

## Detailed Trace

### Step 1: [Entry Point] (file:line)
**Input:** [Parameters]
**Processing:** [What happens]
**Calls:** [Next function]

### Step 2: [Target Function] (file:line)
**Input:** [Parameters]
**Processing:** [What happens]
**Output:** [Return value]
**Side Effects:** [DB writes, API calls, etc.]

[Continue for each step...]

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
- Step 3: O(n) complexity
- Step 4: External API call (potential bottleneck)
```

2. Mark todos complete
3. Offer to dive deeper on specific steps

---

## Error Handling

- **Function not found**: Search for similar names, suggest alternatives
- **Ambiguous name**: Present all matches, ask user to specify file
- **Recursive calls**: Detect and note cycles, don't infinite loop
- **External libraries**: Note when trace enters external code, stop there
