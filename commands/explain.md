---
description: Deep, multi-layered code explanation with parallel context gathering from dependencies, git history, tests, and patterns
argument-hint: File path, directory, or function name [--depth=overview|standard|deep] [--audience=junior|senior|non-technical]
---

# Code Explanation Workflow

You are helping a developer understand code by gathering context from multiple sources and synthesizing a comprehensive explanation.

## Core Principles

- **Gather context first**: Launch parallel agents to collect dependencies, history, tests, and patterns
- **Synthesize, don't summarize**: Combine findings into layered explanation with insights
- **Tailor to audience**: Adjust language and detail based on audience level
- **Use TodoWrite**: Track progress throughout

---

## Phase 1: Parse Request

**Goal**: Understand what to explain and how

Initial request: $ARGUMENTS

**Actions**:
1. Create todo list with all phases
2. Parse the request:
   - **Target**: File path, directory, or function name
   - **Depth**: `overview`, `standard` (default), or `deep`
   - **Audience**: `junior`, `senior` (default), or `non-technical`
3. If target is ambiguous (e.g., function name without file), use Grep to locate it
4. Validate the target exists

**If no arguments provided**, ask user what they want explained.

---

## Phase 2: Context Gathering

**Goal**: Gather comprehensive context from multiple sources in parallel

**CRITICAL**: Launch all 4 agents in a SINGLE message to maximize parallelism.

**Actions**:
1. Launch all 4 gathering agents **in parallel**:

   **context-gatherer agent**:
   - Trace imports, exports, and dependencies
   - Map call hierarchy (callers and callees)
   - Identify configuration that affects behavior
   - Return JSON with: imports, exports, callers, callees, config_dependencies, module_context

   **history-detective agent**:
   - Run git blame on target file
   - Find key commits that introduced/modified the code
   - Identify linked issues/PRs from commit messages
   - Return JSON with: blame, key_commits, linked_issues, refactoring_history, insights

   **test-reader agent**:
   - Find related test files
   - Extract test scenarios and descriptions
   - Identify edge cases being tested
   - Note coverage gaps
   - Return JSON with: test_files, test_scenarios, edge_cases_covered, behaviors_documented, coverage_gaps

   **pattern-identifier agent**:
   - Identify design patterns (Factory, Strategy, Repository, etc.)
   - Detect framework conventions
   - Note project-specific patterns
   - Flag deviations or anti-patterns
   - Return JSON with: design_patterns, framework_conventions, project_conventions, deviations, anti_patterns

2. Wait for all agents to complete
3. Read the target file content

---

## Phase 3: Synthesis

**Goal**: Create the layered explanation

**Actions**:
1. Launch **explanation-synthesizer agent** with:
   - Target file content
   - All 4 JSON outputs from gathering agents
   - Depth and audience parameters

2. The synthesizer will create a markdown explanation following the depth level:

### Overview Depth
- TL;DR paragraph
- Key responsibilities (3-5 bullets)
- Main dependencies

### Standard Depth
- TL;DR
- Architecture context diagram (ASCII)
- Key functions with callers
- Patterns used
- Gotchas and warnings
- Test coverage summary

### Deep Depth
- Everything in standard
- Line-by-line walkthrough of complex logic
- Historical context (who wrote it, why, when)
- Detailed test coverage analysis
- Recommendations for improvement

---

## Phase 4: Present Results

**Goal**: Deliver the explanation to the user

**Actions**:
1. Present the synthesized explanation
2. Mark all todos complete
3. Offer to:
   - Dive deeper on specific sections
   - Generate diagrams
   - Answer follow-up questions

---

## Audience Adaptation

**Junior Developer**:
- Explain terminology and concepts
- Provide more context on patterns
- Step-by-step logic walkthrough

**Senior Developer**:
- Focus on non-obvious details
- Discuss trade-offs and architectural decisions
- Skip basic explanations

**Non-Technical**:
- Avoid jargon entirely
- Use analogies
- Focus on WHAT and WHY, not HOW
- Emphasize business impact

---

## Error Handling

- **File not found**: Search for similar files, suggest alternatives
- **Directory too large**: Suggest specific files or use --depth=overview
- **No tests found**: Note in explanation, suggest creating tests
- **No git history**: Skip historical context, note limitation
