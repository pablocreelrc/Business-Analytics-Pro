# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A Claude Code skill repo for business analytics and decision modeling. Each skill teaches frameworks, math, and business context — then produces professional output in the user's chosen format.

## Skill Conventions

- **Frontmatter:** Anthropic standard — `name` + `description` (pushy, 20+ trigger phrases)
- **Body limit:** <500 lines per SKILL.md. Move heavy theory to `references/` subfolder
- **Required sections:** Purpose, When to Use, Foundation, Process (with mode-specific branching), Excel Output Specification, Python Output Specification, Output, Anti-Patterns, Related Skills
- **Three entry modes:** Guided (step-by-step), Context Dump (user pastes a problem), Quick Draft (user says "just build it")

## Output Mode Routing

Every skill detects user intent and routes to one of four modes. See `references/output-mode-routing.md` for the shared logic.

| Mode | Tool |
|------|------|
| Excel | Shortcut.ai API (`shortcut_excel.py`) — IB-formatted workbook |
| Python | numpy, scipy, PuLP, scikit-learn, matplotlib |
| Both | Python computes, Shortcut.ai formats into Excel |
| Teach | Claude reasoning only, no code output |

## Excel Standards

All Excel output follows IB formatting per `references/excel-standards.md`:
- Calibri 10pt. Blue font for inputs, black for formulas, green for cross-sheet links
- Headers: bold, white on navy. Parentheses for negatives. No $ in body rows
- Assumptions tab always last. Named ranges for key inputs. Gridlines off
- Domain-specific formats per `references/analytics-format-codes.md`
- **Always use Shortcut.ai API** — never openpyxl or xlsxwriter

## Python Standards

- Use `python` (not `python3`)
- Self-contained scripts with inline methodology comments
- Print key results to console, matplotlib charts where visualization adds value

## Categories

`foundations`, `probability`, `simulation`, `optimization`, `forecasting`, `data-mining`, `workflows`, `infrastructure`
