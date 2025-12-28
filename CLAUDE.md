# CLAUDE.md

## Project Overview

This is the **Skills Performance Optimizer** - a Claude Code skill for auditing and optimizing other skills for token efficiency and progressive disclosure compliance.

## Quick Context

When working on this project:
- This IS a Claude Code skill (the `SKILL.md` is the entry point)
- The skill audits OTHER skills, not itself
- Scripts are in `scripts/` and should be executable (`chmod +x`)
- Documentation follows progressive disclosure (brief in SKILL.md, detailed in docs/)

## Key Files

| File | Purpose |
|------|---------|
| `SKILL.md` | Main skill entry point - Claude loads this when skill is triggered |
| `docs/disclosure-levels.md` | Detailed 3-level model specification |
| `docs/validation-rules.md` | All validation rules with severity levels |
| `scripts/analyze-skill.sh` | Primary analysis tool |
| `scripts/validate-skill.sh` | Pass/fail validation |

## Development Commands

```bash
# Test the skill on itself
./scripts/validate-skill.sh . true

# Analyze another skill
./scripts/analyze-skill.sh ~/.claude/skills/some-skill

# Batch audit all skills
./scripts/audit-all-skills.sh ~/.claude/skills

# Install as symlink for development
ln -sf $(pwd) ~/.claude/skills/optimizing-skills-performance
```

## Architecture Principles

### Progressive Disclosure
1. **Level 1 (Metadata)**: Keep description under 1024 chars, include "use when" trigger
2. **Level 2 (SKILL.md body)**: Keep under 500 lines, act as navigation guide
3. **Level 3 (docs/)**: Put detailed specifications here, referenced from SKILL.md

### Validation Severity Levels
- **Critical (C###)**: Blocks skill loading - must fix
- **High (H###)**: Significant token waste - should fix
- **Medium (M###)**: Best practice violations - nice to have
- **Low (L###)**: Style issues - optional

### Scoring Formula
```
Base: 100 points
- Critical violation: -50 each
- High violation: -10 each
- Medium violation: -5 each
- Low violation: -2 each
```

## Common Tasks

### Adding a New Validation Rule

1. Add rule definition to `docs/validation-rules.md`
2. Add check logic to `scripts/validate-skill.sh`
3. Update scoring if needed
4. Test on sample skills

### Updating Token Budgets

Token budgets are defined in:
- `docs/disclosure-levels.md` (authoritative)
- `docs/token-counting.md` (measurement guide)
- `scripts/analyze-skill.sh` (thresholds for warnings)

### Modifying Script Output

All scripts output to stdout in human-readable format. JSON output goes to files:
- `audit-all-skills.sh` writes to `audit-report.json` (or specified file)
- Add `--json` flag support when needed

## Testing

```bash
# Self-validation (should pass with minor warnings)
./scripts/validate-skill.sh . true

# Test on known-good skill
./scripts/analyze-skill.sh ~/.claude/skills/skill-builder

# Test on known-problematic skill (if available)
./scripts/analyze-skill.sh ~/.claude/skills/verbose-skill
```

## Code Style

- Shell scripts: POSIX-compatible bash
- Use `set -e` for error handling
- Prefer readable over clever
- Add comments for non-obvious logic

## Don't Forget

- Keep SKILL.md under 500 lines
- Update README.md for user-facing changes
- Run self-validation before committing
- Scripts must be executable (`chmod +x`)
