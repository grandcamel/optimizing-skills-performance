# Skills Performance Optimizer

A Claude Code skill for auditing and optimizing existing skills for token efficiency and progressive disclosure compliance.

## Overview

This skill analyzes Claude Code skills against the 3-level progressive disclosure model, identifying token waste and providing actionable optimization recommendations.

### The Restaurant Menu Analogy

Think of an optimized skill like a restaurant menu:
- **Level 1 (Metadata)**: The cuisine name on the sign outside
- **Level 2 (SKILL.md)**: The menu itself
- **Level 3 (Nested Resources)**: Ingredients and nutritional facts kept in back

## Installation

### As a Claude Code Skill

```bash
# Copy to your skills directory
cp -R . ~/.claude/skills/optimizing-skills-performance

# Or create a symlink
ln -s $(pwd) ~/.claude/skills/optimizing-skills-performance
```

### Standalone Usage

The scripts can be run directly without installing as a skill:

```bash
./scripts/analyze-skill.sh /path/to/skill
./scripts/audit-all-skills.sh ~/.claude/skills
./scripts/validate-skill.sh /path/to/skill
```

## Quick Start

### Analyze a Single Skill

```bash
./scripts/analyze-skill.sh ~/.claude/skills/my-skill
```

Output includes:
- Level 1 metadata analysis (name/description length)
- Level 2 body metrics (lines, tokens, code blocks)
- Level 3 resource inventory
- Violation detection
- Final score (A+ to F)

### Audit All Skills

```bash
./scripts/audit-all-skills.sh ~/.claude/skills
```

Generates `audit-report.json` with per-skill metrics and scores.

### Validate Before Publishing

```bash
./scripts/validate-skill.sh ~/.claude/skills/my-skill true
```

Returns pass/fail with detailed violation list.

## The 3-Level Disclosure Model

### Level 1: Metadata (Always Loaded)
- **Target**: ~200 chars per skill
- **Loaded**: At startup for ALL skills
- **Content**: `name` and `description` from YAML frontmatter

### Level 2: SKILL.md Body (On-Demand)
- **Target**: <500 lines, <4000 tokens
- **Loaded**: Only when skill is triggered
- **Content**: Quick start, core workflow, navigation links

### Level 3: Nested Resources (Deep Dive)
- **Target**: No limit (loaded on-demand)
- **Loaded**: Only when explicitly accessed
- **Content**: API references, examples, troubleshooting

## Validation Rules

### Critical (Must Fix)
- `C001`: Missing YAML frontmatter
- `C002`: Missing required fields (name, description)
- `C003`: Description exceeds 1024 characters
- `C004`: Name exceeds 64 characters
- `C005`: Invalid directory structure

### High Severity (Should Fix)
- `H001`: Missing trigger condition ("Use when...")
- `H002`: SKILL.md exceeds 500 lines
- `H003`: Deep reference nesting (A -> B -> C)
- `H004`: Large inline code blocks (>50 lines)
- `H005`: Voodoo constants (explaining what Claude knows)

### Medium Severity (Nice to Have)
- `M001`: Missing Quick Start section
- `M002`: Non-gerund directory name
- `M003`: Inconsistent naming
- `M004`: Missing prerequisites

## Scoring System

| Grade | Score | Criteria |
|-------|-------|----------|
| A+ | 95-100 | 0 critical, 0 high, ≤2 medium |
| A | 90-94 | 0 critical, 0 high, ≤4 medium |
| B | 80-89 | 0 critical, ≤2 high |
| C | 70-79 | 0 critical, ≤4 high |
| D | 60-69 | 0 critical, >4 high |
| F | 0-59 | Any critical violations |

## Project Structure

```
optimizing-skills-performance/
├── SKILL.md                 # Main skill file (Claude loads this)
├── README.md                # This file (human documentation)
├── CLAUDE.md                # Instructions for Claude
├── docs/
│   ├── disclosure-levels.md # Full 3-level specification
│   ├── token-counting.md    # Measurement techniques
│   ├── naming-conventions.md # Naming patterns
│   └── validation-rules.md  # Complete rule set
├── scripts/
│   ├── analyze-skill.sh     # Single skill analysis
│   ├── audit-all-skills.sh  # Batch audit
│   └── validate-skill.sh    # Pass/fail validation
└── resources/               # (Reserved for templates)
```

## Key Concepts

### Voodoo Constants
Explanations of concepts Claude already knows (e.g., "JSON stands for JavaScript Object Notation"). Delete these to save tokens.

### Deep Nesting
When docs reference other docs (A -> B -> C), Claude may only preview files. Keep references one level deep from SKILL.md.

### Gerund Naming
Use present participle form for action-oriented skills:
- `analyzing-spreadsheets` (not `spreadsheet-analyzer`)
- `generating-apis` (not `api-generator`)

## Contributing

1. Fork this repository
2. Create a feature branch
3. Run validation on your changes: `./scripts/validate-skill.sh . true`
4. Submit a pull request

## License

MIT License - See LICENSE file for details.

## Related Resources

- [Claude Code Documentation](https://docs.anthropic.com/claude-code)
- [Agent Skills Specification](https://docs.anthropic.com/agent-skills)
