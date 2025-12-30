---
name: explain
description: This skill should be used when the user asks to "explain this code", "help me understand this file", "what does this module do", "walk me through this", "break down this code", or uses "/explain". Provides deep, multi-layered code explanations by orchestrating parallel context-gathering agents.
---

# Code Explanation Skill

Provide deep, multi-layered code explanations by gathering context from multiple sources in parallel and synthesizing a comprehensive explanation.

## Invocation

```
/explain <target> [--depth=overview|standard|deep] [--audience=junior|senior|non-technical]
```

**Parameters:**
- `target` - File path, directory, or function name (required)
- `--depth` - Explanation depth: `overview` (default), `standard`, or `deep`
- `--audience` - Target audience: `junior`, `senior` (default), or `non-technical`

**Examples:**
```
/explain src/auth/oauth.ts
/explain src/services/payment/ --depth=deep
/explain handleCheckout --audience=junior
```

## Workflow

Execute this workflow when the skill triggers:

### Step 1: Parse Target and Options

Extract from user input:
1. Target path or function name
2. Depth level (default: standard)
3. Audience level (default: senior)

If target is ambiguous (e.g., function name without file), use Grep to locate it first.

### Step 2: Launch Parallel Agents

Use the Task tool to launch ALL 5 agents in parallel in a SINGLE message with multiple tool calls:

```
Launch simultaneously:
1. context-gatherer - Analyze dependencies, imports, call hierarchy
2. history-detective - Git blame, commits, linked issues
3. test-reader - Find tests, extract scenarios and edge cases
4. pattern-identifier - Identify design patterns and conventions
```

**Critical:** Launch all 4 gathering agents in parallel (single message, multiple Task tool calls) to minimize latency.

Each agent will return structured JSON. Wait for all 4 to complete.

### Step 3: Launch Synthesizer

After all 4 gathering agents complete, launch the explanation-synthesizer agent with:
- The target file content (read it first)
- All 4 JSON outputs from gathering agents
- The depth and audience parameters

Provide this information in the agent prompt:
```
Synthesize an explanation for: [target]
Depth: [depth]
Audience: [audience]

Context Data:
[JSON from context-gatherer]

History Data:
[JSON from history-detective]

Test Data:
[JSON from test-reader]

Pattern Data:
[JSON from pattern-identifier]

Target File Content:
[file content]
```

### Step 4: Present Result

The explanation-synthesizer will return a formatted markdown explanation. Present this directly to the user.

## Depth Levels

**Overview:**
- TL;DR paragraph
- Key responsibilities (3-5 bullets)
- Main dependencies

**Standard:**
- TL;DR
- Architecture context diagram
- Key functions with callers
- Patterns used
- Gotchas and warnings
- Test coverage summary

**Deep:**
- Everything in standard
- Line-by-line walkthrough of complex logic
- Historical context (who wrote it, why, when)
- Detailed test coverage analysis
- Recommendations for improvement

## Audience Adaptation

**Junior:** Explain terminology, provide pattern context, step-by-step logic

**Senior:** Focus on non-obvious details, trade-offs, architectural decisions

**Non-Technical:** Avoid jargon, use analogies, focus on business impact

## Error Handling

- **File not found:** Search for similar files and suggest alternatives
- **Directory too large:** Suggest specific files or use --depth=overview
- **No tests found:** Note in explanation, suggest creating tests
- **No git history:** Skip historical context, note limitation

## Output Format

The final output should be a complete markdown document following the structure defined by the depth level, including:
- TL;DR section
- Architecture diagrams (ASCII)
- Function/class documentation
- Pattern analysis
- Gotchas and warnings
- Test coverage insights
