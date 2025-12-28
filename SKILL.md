---
name: "Skills Performance Optimizer"
description: "Audit and optimize existing skills for token efficiency and progressive disclosure compliance. Use when analyzing skill token footprint, reformatting verbose skills into hierarchical structures, or ensuring skills follow the 3-level disclosure model."
---

# Skills Performance Optimizer

## What This Skill Does

Audits existing skills for token efficiency and progressive disclosure compliance, then provides actionable reformatting recommendations. Think of it like a restaurant menu: metadata is the sign outside (Level 1), SKILL.md is the menu (Level 2), and nested resources are ingredients kept in back (Level 3).

## Prerequisites

- Skills located in `~/.claude/skills/` or `.claude/skills/`
- Basic understanding of YAML and Markdown
- Claude Code 2.0+

## Quick Start

### Audit a Single Skill
```bash
# Analyze a skill's token efficiency
./scripts/analyze-skill.sh ~/.claude/skills/my-skill

# Output: Token count, disclosure level violations, recommendations
```

### Audit All Skills
```bash
# Scan entire skills directory
./scripts/audit-all-skills.sh ~/.claude/skills

# Generates: audit-report.json with per-skill scores
```

### Quick Validation Checklist
Use this checklist when reviewing any skill:

- [ ] **Level 1**: Description < 1024 chars, third-person, clear triggers
- [ ] **Level 2**: SKILL.md body < 500 lines, acts as navigation guide
- [ ] **Level 3+**: Detailed content moved to separate files
- [ ] **No deep nesting**: References are one level deep from SKILL.md
- [ ] **No voodoo constants**: Removed explanations Claude already knows

---

## The 3-Level Disclosure Model

### Level 1: Metadata (Always Loaded)
**Target**: ~200 chars per skill (dozens of tokens)
**Loaded**: At startup for ALL skills

```yaml
---
name: "API Builder"           # Max 64 chars
description: "Creates REST... # Max 1024 chars, include WHAT + WHEN
---
```

**Optimization**: If 100 skills each use 200 chars metadata = ~6KB total context. This is acceptable.

### Level 2: SKILL.md Body (On-Demand)
**Target**: 1-10KB, under 500 lines
**Loaded**: Only when skill is triggered

The body should be a **navigation guide**, not a reference manual:
- Quick Start (80% use case)
- High-level workflow overview
- Links to detailed docs

### Level 3+: Nested Resources (Deep Dive)
**Target**: Variable (KB to MB)
**Loaded**: Only when explicitly accessed

Move here:
- API references
- Complex code examples
- Troubleshooting guides
- Configuration schemas

---

## Optimization Workflow

### Step 1: Measure Token Footprint

```bash
# Count tokens in SKILL.md
wc -w ~/.claude/skills/my-skill/SKILL.md

# Target: < 2000 words for Level 2 content
```

### Step 2: Identify Violations

| Violation | Detection | Fix |
|-----------|-----------|-----|
| Bloated description | > 1024 chars | Trim, front-load keywords |
| Over-explained concepts | Explains what a PDF/JSON/API is | Delete (Claude knows) |
| Inline code dumps | > 50 line code blocks | Move to `resources/` |
| Deep nesting | File A -> B -> C | Flatten to one level |
| Missing triggers | No "use when" clause | Add specific trigger conditions |

### Step 3: Apply Reformatting

**Before** (Verbose):
```markdown
## What is JSON?
JSON (JavaScript Object Notation) is a lightweight data format...
[300 words explaining JSON]

## How to Parse JSON
```javascript
// Full 200-line parsing implementation
```
```

**After** (Optimized):
```markdown
## JSON Handling
Parse config: `node scripts/parse-config.js input.json`

For advanced parsing options, see [JSON Reference](docs/json-reference.md).
```

### Step 4: Validate Structure

Run the validation script:
```bash
./scripts/validate-skill.sh ~/.claude/skills/my-skill
```

Checks:
- YAML frontmatter present and valid
- Description includes "what" and "when"
- No files deeper than one level from SKILL.md
- Gerund-form naming (if applicable)

---

## Common Anti-Patterns

### 1. Voodoo Constants
**Problem**: Explaining things Claude already knows
```markdown
# BAD
PDF files (Portable Document Format) are binary files that...
```
**Fix**: Delete. Claude knows what PDFs are.

### 2. Monolithic Scripts
**Problem**: Embedding full scripts in SKILL.md
```markdown
# BAD
```python
# 500-line script inline
```
```
**Fix**: Move to `scripts/` and reference by path. Claude executes without loading source.

### 3. Deep Reference Chains
**Problem**: SKILL.md -> docs/setup.md -> docs/advanced/config.md
**Fix**: Flatten to SKILL.md -> docs/advanced-config.md (one level)

### 4. Documentation Over Workflow
**Problem**: Long explanatory paragraphs
**Fix**: Use checklists Claude can copy and track

---

## Audit Report Format

When analyzing skills, generate reports in this structure:

```json
{
  "skill": "my-skill",
  "tokenFootprint": {
    "level1": 180,
    "level2": 3200,
    "level3": 15000
  },
  "violations": [
    {
      "type": "bloated_description",
      "severity": "high",
      "location": "frontmatter",
      "current": 1847,
      "target": 1024,
      "recommendation": "Trim description, front-load keywords"
    }
  ],
  "score": 72,
  "grade": "C"
}
```

---

## Reference Documentation

For detailed criteria and advanced workflows:

- [Disclosure Level Criteria](docs/disclosure-levels.md) - Full specification for each level
- [Token Counting Guide](docs/token-counting.md) - Accurate measurement techniques
- [Naming Conventions](docs/naming-conventions.md) - Gerund forms and patterns
- [Validation Rules](docs/validation-rules.md) - Complete rule set

## Scripts

- `scripts/analyze-skill.sh` - Analyze single skill
- `scripts/audit-all-skills.sh` - Batch audit
- `scripts/validate-skill.sh` - Validation checks
- `scripts/reformat-skill.sh` - Automated reformatting suggestions

---

**Version**: 1.0.0
**Category**: performance
