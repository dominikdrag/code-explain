# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commit Convention

Use [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/):

```
<type>: <description>

[optional body]
```

Types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`

Examples:
- `feat: add /trace skill for execution flow tracing`
- `fix: correct JSON schema in context-gatherer output`
- `docs: update README with diagram examples`

## Overview

This is a Claude Code plugin that provides deep, multi-layered code explanations. It uses parallel agent orchestration to gather context from multiple sources before synthesizing explanations.

## Architecture

### Agent Pipeline

The `/explain` skill orchestrates 5 agents:
1. **Gathering phase** (parallel): context-gatherer, history-detective, test-reader, pattern-identifier
2. **Synthesis phase**: explanation-synthesizer consumes JSON from all 4 gatherers

Agents output structured JSON schemas for consistent interchange. The synthesizer (opus) combines these into markdown explanations.

### Model Assignment

- **haiku**: context-gatherer, history-detective, test-reader (fast parallel exploration)
- **sonnet**: pattern-identifier (balanced analysis)
- **opus**: explanation-synthesizer (high-quality synthesis)

### Component Locations

- `agents/` - Agent definitions with frontmatter (name, description, model, color, tools) and system prompts
- `skills/*/SKILL.md` - Skill definitions with frontmatter (name, description) and workflow instructions
- `.claude-plugin/plugin.json` - Plugin manifest

## Plugin Conventions

### Agent Files

Agents use YAML frontmatter with required fields: `name`, `description`, `model`, `color`. The `description` must include `<example>` blocks showing triggering scenarios.

### Skill Files

Skills use third-person descriptions with trigger phrases ("This skill should be used when..."). Body content uses imperative form.

### JSON Output Schemas

Gathering agents return structured JSON:
- context-gatherer: imports, exports, callers, callees, config_dependencies, module_context
- history-detective: blame, key_commits, linked_issues, refactoring_history, insights
- test-reader: test_files, test_scenarios, edge_cases_covered, behaviors_documented, coverage_gaps
- pattern-identifier: design_patterns, framework_conventions, project_conventions, deviations, anti_patterns
