# Changelog

## [1.0.1] - 2025-12-30

### Changed
- Converted skills to commands for explicit user invocation
  - `/explain` - Full explanation with parallel context gathering
  - `/trace` - Trace execution flow through codebase
  - `/why` - Quick explanation of why specific code exists
  - `/diagram` - Generate visual diagrams
- Updated installation guide to use marketplace format
- Added CLAUDE.md with conventional commits convention

### Removed
- Removed `skills/` directory in favor of `commands/`

## [1.0.0] - 2025-12-30

### Added
- Initial plugin release
- `context-gatherer` agent (Haiku) for dependency and call hierarchy analysis
- `history-detective` agent (Haiku) for git history and blame analysis
- `test-reader` agent (Haiku) for test scenario extraction
- `pattern-identifier` agent (Sonnet) for design pattern recognition
- `explanation-synthesizer` agent (Opus) for comprehensive explanation generation
- Parallel agent orchestration for fast context gathering
- Three explanation depth levels: overview, standard, deep
- Three audience levels: junior, senior, non-technical
- Four diagram types: dependencies, sequence, flowchart, class
- ASCII and Mermaid diagram output formats
