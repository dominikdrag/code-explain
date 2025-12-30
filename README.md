# code-explain

Deep, multi-layered code explanations—from high-level architecture down to line-by-line details.

## Features

- **Multi-layered explanations**: Overview, standard, or deep analysis
- **Context gathering**: Traces dependencies, call hierarchy, and configurations
- **History analysis**: Git blame, commit history, linked issues
- **Test insights**: Uses tests as documentation of intent
- **Pattern recognition**: Identifies design patterns and conventions
- **Visual diagrams**: ASCII/Mermaid dependency, sequence, flowchart, and class diagrams

## Skills

| Skill | Description |
|-------|-------------|
| `/explain [target]` | Full explanation with parallel context gathering |
| `/trace [function]` | Trace execution flow through codebase |
| `/why [file:line]` | Quick explanation of why specific code exists |
| `/diagram [target]` | Generate visual diagrams |

## Usage

```bash
# Full explanation with depth control
/explain src/auth/oauth.ts
/explain src/services/payment/ --depth=deep
/explain src/utils/retry.ts --audience=junior

# Trace function execution
/trace handleCheckout
/trace processPayment --max-depth=5

# Quick line-level explanation
/why src/utils/retry.ts:42

# Generate diagrams
/diagram src/services/ --type=dependencies
/diagram handlePayment --type=sequence --format=mermaid
```

## Parameters

### /explain
- `target` - File, directory, or function to explain
- `--depth` - `overview` | `standard` (default) | `deep`
- `--audience` - `junior` | `senior` (default) | `non-technical`

### /trace
- `function` - Function or method name to trace
- `--max-depth` - Maximum call depth (default: 10)

### /why
- `file:line` - File path and line number (e.g., `src/auth.ts:42`)

### /diagram
- `target` - File, directory, or function
- `--type` - `dependencies` | `sequence` | `flowchart` | `class`
- `--format` - `ascii` (default) | `mermaid`

## How It Works

The `/explain` skill orchestrates 5 specialized agents in parallel:

1. **context-gatherer** - Traces imports, exports, callers, callees
2. **history-detective** - Analyzes git blame and commit history
3. **test-reader** - Finds related tests and extracts scenarios
4. **pattern-identifier** - Recognizes design patterns and conventions
5. **explanation-synthesizer** - Combines findings into layered explanation

## Installation

Available via [dominikdrag-marketplace](https://github.com/dominikdrag/dominikdrag-marketplace). Run from Claude Code CLI:

```
/plugin marketplace add dominikdrag/dominikdrag-marketplace
/plugin install code-explain@dominikdrag-marketplace
```

## License

MIT
