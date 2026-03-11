---
name: Skill Authoring Workflow
description: >
  Creates new SKILL.md files for Business-Analytics-Pro. Use this skill when you need to
  author a skill, write a skill, add a new skill, create a SKILL.md, build a skill file,
  draft a skill template, scaffold a skill, generate a skill document, design a skill spec,
  define a new analytical skill, set up a skill folder, bootstrap a skill, initialize a skill,
  skill format guide, skill conventions, skill quality checklist, skill standards,
  how to write a skill, skill authoring best practices, new skill setup, skill structure,
  skill frontmatter, skill body sections, skill anti-patterns template,
  add capability to the repo, extend the skill catalog, register a new skill,
  skill template for business analytics, output mode routing for skills,
  workflow skill template, infrastructure skill template, meta skill
---

# Skill Authoring Workflow

## Purpose

Standardizes the creation of new SKILL.md files in Business-Analytics-Pro so every skill follows the same conventions, triggers reliably, routes to the correct output mode, and produces consistent professional output.

## When to Use

- You are adding a brand-new analytical, simulation, optimization, or workflow skill to this repo
- You need to remember the required SKILL.md sections and formatting rules
- You want the frontmatter template so the skill triggers correctly in Claude Code
- You are reviewing a draft SKILL.md against the quality checklist before merging
- You need to register a new skill in CATALOG.md and marketplace.json
- You want to understand the output mode routing system and how skills branch between Excel, Python, Both, and Teach modes

## Required Frontmatter Format

Every SKILL.md begins with YAML frontmatter containing exactly two fields:

```yaml
---
name: [Skill Name]
description: >
  [Pushy description. Start with what the skill does in one clause, then list 20+
  trigger phrases separated by commas. Include synonyms, alternate phrasings,
  common misspellings, and related jargon. The goal is to PREVENT undertriggering --
  if a user could reasonably mean this skill, a phrase here should match.]
---
```

Rules:
- `name` is the human-readable skill name (title case)
- `description` uses the YAML `>` folded scalar for multi-line text
- Include 20+ trigger phrases minimum -- err on the side of too many
- Trigger phrases should cover: exact name, synonyms, verb forms ("build a...", "run a..."), common misspellings, related jargon, and course-specific terminology

## Required Body Sections

Every analytical skill MUST include all of the following sections. Workflow and infrastructure skills may omit or adapt sections as noted.

| # | Section | Required For | Notes |
|---|---------|-------------|-------|
| 1 | `# [Skill Name]` | All | H1 heading matching the frontmatter `name` |
| 2 | `## Purpose` | All | One sentence. What does this skill teach and produce? |
| 3 | `## When to Use` | All | 5-7 bullets, each a distinct scenario |
| 4 | `## Foundation` | Analytical skills | Frameworks, formulas, theory. Omit for workflows and infrastructure |
| 5 | `## Process` | All | Entry mode selection + build steps. See template below |
| 6 | `## Excel Output Specification` | Analytical + workflow | Tab-by-tab spec with columns, formats, formulas |
| 7 | `## Python Output Specification` | Analytical + workflow | Libraries used, output format, chart types |
| 8 | `## Output` | All | Numbered list of deliverables |
| 9 | `## Anti-Patterns` | All | 8+ specific mistakes in a table |
| 10 | `## Related Skills` | All | Links to prerequisite and follow-on skills |

## Skill Template

Copy the template below when creating a new skill. Replace all `[bracketed]` placeholders. Delete any section comments (lines starting with `>>`) before finalizing.

````markdown
---
name: [Skill Name]
description: >
  [Pushy description with 20+ trigger phrases.]
---

# [Skill Name]

## Purpose

[One sentence. What does this skill teach and produce?]

## When to Use

- [Scenario 1 -- the most common reason a user invokes this skill]
- [Scenario 2]
- [Scenario 3]
- [Scenario 4]
- [Scenario 5]
- [Scenario 6 -- optional, add more if genuinely distinct]
- [Scenario 7 -- optional]

## Foundation

>> Teach the frameworks, formulas, and theory that underpin the skill.
>> Use tables, formulas in code blocks, and worked examples.
>> If this section would push the file over 500 lines, move heavy theory into
>> a `references/` subfolder and summarize here with a link.

### [Framework or Concept 1]

[Explanation with formula blocks, tables, and worked examples.]

### [Framework or Concept 2]

[Explanation.]

## Process

### Entry Mode Selection

When the user invokes this skill, determine which mode fits:

**Guided Mode** -- The user wants to be walked through step by step.
1. [First question to ask]
2. [Second question]
3. [Continue until all inputs are gathered]

**Context Dump Mode** -- The user pastes a problem, dataset, or case excerpt.
1. Parse the inputs from what was provided.
2. State what you found and any assumptions.
3. Ask one clarifying question if a critical input is ambiguous, then build.

**Quick Draft Mode** -- The user says "just build it" or provides numbers inline.
Take what is given, fill reasonable defaults (state them clearly), produce output.

### Output Mode Detection

Detect the user's preferred output mode per `references/output-mode-routing.md`:

| Mode | What Happens |
|------|-------------|
| Excel | Shortcut.ai builds an IB-formatted workbook |
| Python | Self-contained script with relevant libraries |
| Both | Python computes, Shortcut.ai formats into Excel |
| Teach | Walk through framework and math, no code output |

### Adaptive Questioning

| Input | Required For | Default If Missing |
|-------|-------------|-------------------|
| [Input 1] | [Which method/tab] | [Default or "Ask -- no default"] |
| [Input 2] | [Which method/tab] | [Default or "Ask -- no default"] |

### Build Steps

1. Create the Excel workbook with [N] tabs (see Excel Output Specification).
2. Enter all hardcoded inputs on the Assumptions tab in blue font.
3. Build all formulas referencing the Assumptions tab (cross-sheet links in green).
4. Apply IB formatting per `references/excel-standards.md`.
5. Present the result with a brief interpretation.

## Excel Output Specification

>> Specify each tab: name, layout, columns with headers, formats, and formulas.
>> Reference `references/excel-standards.md` for all formatting rules.

### Tab 1: [Analysis Name]

| Column | Header | Format | Formula or Input |
|--------|--------|--------|-----------------|
| A | [Header] | [Excel format code] | [Formula text or "Input (blue)"] |
| B | [Header] | [Excel format code] | [Formula text] |

### Tab N: Assumptions & Inputs

>> Always the last tab. All hardcoded values live here.
>> Blue font, yellow background on key assumptions.
>> Define named ranges for anything referenced by other tabs.

| Cell | Label | Default Value | Format |
|------|-------|--------------|--------|
| B2 | [Assumption 1] | [value] | [format] |
| B3 | [Assumption 2] | [value] | [format] |

Named ranges: `[Name]` -> B2, `[Name]` -> B3.

## Python Output Specification

When Python mode is selected, produce a self-contained script using:
- [List relevant libraries: numpy, scipy, pulp, scikit-learn, matplotlib, etc.]
- Include inline comments explaining the methodology
- Print key results to console
- Generate matplotlib charts where visualization adds value
- Use `python` (not `python3`)

## Output

This skill produces:

1. **[Primary deliverable]** -- [description]
2. **[Secondary deliverable]** -- [description]
3. **[Interpretation]** -- [description]

## Anti-Patterns

| Mistake | Why It Is Wrong | Correct Approach |
|---------|----------------|-----------------|
| [Specific mistake 1] | [Concrete consequence] | [Exact fix] |
| [Specific mistake 2] | [Concrete consequence] | [Exact fix] |
| [Specific mistake 3] | [Concrete consequence] | [Exact fix] |
| [Specific mistake 4] | [Concrete consequence] | [Exact fix] |
| [Specific mistake 5] | [Concrete consequence] | [Exact fix] |
| [Specific mistake 6] | [Concrete consequence] | [Exact fix] |
| [Specific mistake 7] | [Concrete consequence] | [Exact fix] |
| [Specific mistake 8] | [Concrete consequence] | [Exact fix] |

## Related Skills

- **[Prerequisite Skill]** (`category/skill-name/`) -- [One-line relationship description]
- **[Follow-on Skill]** (`category/skill-name/`) -- [One-line relationship description]
````

## Quality Checklist

Run through every item before considering a SKILL.md ready to merge. A "No" on any item means the skill needs revision.

| # | Check | Pass? |
|---|-------|-------|
| 1 | **Frontmatter `description` is pushy** -- contains 20+ trigger phrases including synonyms, alternate phrasings, and related jargon | |
| 2 | **Under 500 lines** -- heavy theory moved to `references/` subfolder if needed | |
| 3 | **All three entry modes documented** -- Guided, Context Dump, and Quick Draft each have clear instructions | |
| 4 | **Output mode routing included** -- Process section references `references/output-mode-routing.md` and handles Excel, Python, Both, and Teach modes | |
| 5 | **Excel Output Specification present** -- every analytical/workflow skill specifies tab names, column headers, Excel format codes, and formula text | |
| 6 | **Python Output Specification present** -- lists libraries, output format, chart types | |
| 7 | **Assumptions tab defined** -- last tab, blue font, yellow background on key cells, named ranges listed | |
| 8 | **Formatting references `references/excel-standards.md`** and restates the IB color rules (blue/black/green) | |
| 9 | **Anti-patterns are specific** -- 8+ entries, each naming a concrete mistake with consequence and fix (not generic advice) | |
| 10 | **Related Skills use correct relative paths** -- format is `category/skill-name/` matching actual folder structure | |
| 11 | **Purpose is one sentence** -- not a paragraph, not a bullet list | |
| 12 | **When to Use has 5-7 bullets** -- each is a distinct scenario, not a restatement of the same idea | |

## Categories

When creating a new skill, place it in one of the following categories. Each maps to a top-level folder in the repo:

| Category | Folder | What Belongs Here |
|----------|--------|------------------|
| Foundations | `foundations/` | Core modeling skills: spreadsheet modeling, breakeven analysis, NPV/IRR |
| Probability | `probability/` | Probability distributions, decision analysis, Bayes' theorem |
| Simulation | `simulation/` | Monte Carlo simulation, simulation models, correlation modeling |
| Optimization | `optimization/` | Linear programming, integer programming, portfolio optimization |
| Forecasting | `forecasting/` | Time series, exponential smoothing, ARIMA |
| Data Mining | `data-mining/` | Classification, clustering, association rules, market basket |
| Workflows | `workflows/` | Multi-skill pipelines that chain component skills in sequence |
| Infrastructure | `infrastructure/` | Meta-skills for repo maintenance (this skill, templates, validators) |

## Process for Adding a New Skill

### Step 1: Create the Folder and File

```
category/skill-name/SKILL.md
```

Use lowercase kebab-case for the skill name. If the skill requires heavy reference material (long derivations, large lookup tables, extended examples), create a `references/` subfolder:

```
category/skill-name/
  SKILL.md
  references/
    theory-deep-dive.md
    example-dataset.csv
```

### Step 2: Write the SKILL.md

Copy the Skill Template above. Fill in every section. Delete the section comments (lines starting with `>>`). Run through the Quality Checklist.

### Step 3: Add to CATALOG.md

Open `CATALOG.md` at the repo root and add an entry in the appropriate category section:

For analytical skills:
```markdown
| [N] | [skill-name](category/skill-name/SKILL.md) | Interactive | tag1, tag2, tag3 |
```

For workflow skills:
```markdown
| [N] | [skill-name](workflows/skill-name/SKILL.md) | skill-a -> skill-b -> skill-c |
```

### Step 4: Update marketplace.json

If the repo uses `marketplace.json` for programmatic skill discovery, add an entry:

```json
{
  "name": "skill-name",
  "category": "category",
  "path": "category/skill-name/SKILL.md",
  "description": "One-sentence description matching CATALOG.md"
}
```

### Step 5: Test the Skill

Invoke the skill with sample data to verify:

1. The description triggers correctly (try 3-4 different phrasings)
2. All three entry modes produce sensible behavior
3. Output mode routing works for Excel, Python, Both, and Teach
4. The Excel output matches the specification and follows `references/excel-standards.md`
5. Changing an assumption on the Assumptions tab cascades through all dependent cells
6. Anti-patterns listed are genuinely things the skill avoids

## Output Mode Routing Reference

All skills share the same output mode detection logic defined in `references/output-mode-routing.md`. Summary:

| Priority | Signal Keywords | Mode |
|----------|----------------|------|
| 1 (highest) | "both", "build and run", "Excel and Python" | Both |
| 2 | "Excel", "spreadsheet", "model", "workbook", "Shortcut" | Excel |
| 2 | "Python", "script", "run", "simulate", "compute", "calculate" | Python |
| 3 | "explain", "walk me through", "how", "teach", "what is" | Teach |
| 4 (default) | No signal | Ask user |

## Excel Standards Reference

All Excel output follows IB formatting via Shortcut.ai API (`shortcut_excel.py`). Full spec in `references/excel-standards.md`. Key rules:

| Element | Font | Color | Fill |
|---------|------|-------|------|
| Hardcoded inputs | Calibri 10pt | Blue (0,0,255) | Yellow |
| Formulas | Calibri 10pt | Black (0,0,0) | None |
| Cross-sheet links | Calibri 10pt | Green (0,128,0) | None |
| Headers | Calibri 10pt Bold | White | Dark blue / navy |

Number formats per `references/analytics-format-codes.md`. Negatives in parentheses. No $ in body rows. Gridlines off. Freeze panes on headers.

**Always use Shortcut.ai API** -- never openpyxl or xlsxwriter.

## Anti-Patterns

| Mistake | Why It Is Wrong | Correct Approach |
|---------|----------------|-----------------|
| Writing a vague frontmatter description with fewer than 20 trigger phrases | Skill will not trigger when users ask for it in natural language | Pack the description with 20+ phrases covering synonyms, verb forms, jargon, and misspellings |
| Exceeding 500 lines in a single SKILL.md | File becomes unwieldy; Claude Code may truncate or slow down | Move heavy theory to a `references/` subfolder; keep SKILL.md under 500 lines |
| Omitting the Output Mode Detection section | Skill defaults to one mode and ignores user intent | Include the mode routing table referencing `references/output-mode-routing.md` |
| Writing generic anti-patterns like "be careful with data" | Provides no actionable guidance; fails to prevent real mistakes | Name a specific mistake, explain the concrete consequence, and give the exact fix |
| Forgetting to define named ranges on the Assumptions tab | Formulas use cell references that break when rows are inserted | Define named ranges for every assumption and use them in all formulas |
| Placing the Assumptions tab first instead of last | Violates repo convention; confuses users who expect analysis tabs first | Assumptions tab is always the last tab in the workbook |
| Copying a SKILL.md from another skill without updating Related Skills paths | Broken links and incorrect cross-references | Verify every path in Related Skills matches the actual folder structure |
| Skipping the Quality Checklist before merging | Missing sections, broken formatting, or undertriggering go unnoticed | Run all 12 checklist items and fix every "No" before considering the skill done |

## Related Skills

- **Spreadsheet Modeling** (`foundations/spreadsheet-modeling/`) -- Canonical example of a well-structured analytical skill
- **Investment Analysis** (`workflows/investment-analysis/`) -- Example of a workflow skill that chains multiple component skills
- **Project Valuation** (`workflows/project-valuation/`) -- Another workflow skill example with real options focus
