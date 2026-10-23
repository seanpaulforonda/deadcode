# deadcode

[![Agent Skills](https://img.shields.io/badge/Agent%20Skills-Standard-black?style=flat-square)](https://agentskills.io)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=flat-square)](LICENSE)

deadcode gives AI coding agents a disciplined, evidence first auditing engine. Instead of guessing or making reckless deletions, it maps uncalled functions, unused imports, dead CSS selectors, and abandoned code blocks across your entire repository, presenting a structured report for approval before any changes are made.

Compatible with Antigravity, Claude Code, and other AI coding agents.

---

## Overview
`deadcode` is a lightweight audit skill for AI coding agents across any programming language. It scans repositories for uncalled functions, unused imports, dead CSS selectors, unreachable branches, and abandoned code blocks, delivering a structured report with line numbers before touching any file.

Standard coding agents often delete or refactor code too aggressively. `deadcode` follows an audit first philosophy, proving zero call sites with static analysis and waiting for explicit user approval before performing surgical removals.

---

## Features
* **Uncalled Definitions**: Scans for declared functions, classes, methods, and structs that have zero references across the codebase.
* **Unreachable Logic**: Flags dead statements positioned after returns, throws, breaks, continues, or hardcoded false conditions.
* **Unused Imports and Dependencies**: Detects imported packages, modules, and header references never used in file bodies.
* **Duplicate Rules and Declarations**: Identifies duplicate CSS media queries, repeated class definitions, and overwritten function declarations.
* **Abandoned Commented Code**: Surfaces large blocks of stale commented code that add noise to active files.

---

## Supported Languages & Native Tools

When invoked, `deadcode` detects your project stack and uses native linters if present, falling back to static AST and symbol reference queries if no linter is installed:

| Language / Stack | Native Tools (If Available) | Fallback Static Analysis |
| :--- | :--- | :--- |
| **JavaScript / TypeScript** | `eslint`, `ts-prune`, `knip`, `depcheck` | Cross repo declaration to call site search |
| **Python** | `vulture`, `flake8`, `ruff`, `autoflake` | Unreferenced definitions and import search |
| **HTML / CSS** | `purgecss`, `stylelint` | Selector to DOM matching, duplicate media query checks |
| **Rust** | `cargo check`, `cargo-udeps` | Unused compiler warning audit |
| **Go** | `go vet`, `deadcode`, `golangci-lint` | Compiler reference analysis |
| **Java / Kotlin** | `spotbugs`, `pmd` | Static symbol reference check |
| **C / C++** | `cppcheck`, `clang-tidy` | Header and translation unit reference analysis |
| **PHP / Ruby** | `phpstan`, `rubocop` | Method symbol lookup |

---

## The 4 Phase Audit Procedure

1. **Reconnaissance**: Detects project languages, active package managers, configuration files, and main entry points.
2. **Static Analysis**: Maps symbol references and scans for the five categories of dead code.
3. **Structured Audit Report**: Displays exact file paths, line ranges, code signatures, and safety risk levels.
4. **Approval and Verification**: Applies minimal deletions only for approved items, followed by a compiler or syntax check to guarantee zero regressions.

---

## Installation

### For Antigravity
Clone this repository into your local Antigravity skills directory:
```bash
git clone https://github.com/itszshinnn/deadcode.git C:\Users\<Username>\.gemini\config\skills\deadcode
```

### For Claude Code
Clone into your global or project skills folder:
```bash
git clone https://github.com/itszshinnn/deadcode.git ~/.claude/skills/deadcode
```

### Quick Install via Agent Prompt
Paste this prompt directly into your agent chat session:
```text
Read https://github.com/itszshinnn/deadcode and install the deadcode skill into my agent skills directory so I can run /deadcode.
```

---

## Usage

Trigger the scan at any time by typing the slash command in chat:
```text
/deadcode
```

Or ask naturally:
```text
Scan and audit this project for unused code and leftover declarations.
```

### Sample Audit Report Output
```markdown
### Project Stack Detected
* **Environment:** TypeScript / Next.js / Tailwind CSS
* **Files scanned:** 42 files

### Audit Report: 3 Items Found

#### 1. Uncalled Function
* **File:** `src/utils/formatting.ts` (Lines 48 to 62)
* **Code:** `export function formatLegacyDate(val: string)`
* **Finding:** Zero references found across the repository.
* **Risk level:** Zero risk

#### 2. Unused Import
* **File:** `src/components/Navigation.tsx` (Line 3)
* **Code:** `import { useMemo } from 'react';`
* **Finding:** `useMemo` is imported but never called.
* **Risk level:** Zero risk

#### 3. Duplicate CSS Media Query
* **File:** `src/styles/globals.css` (Lines 112 to 125)
* **Code:** `@media (max-width: 768px) { ... }`
* **Finding:** Redundant duplicate query identical to rules at line 74.
* **Risk level:** Zero risk

### Summary of Impact
* **Total dead lines identified:** 31 lines

### Next Step
Would you like me to remove all 3 items, or would you like to keep specific ones?
```

---

## Core Philosophy

* **Evidence before accusation**: Never flag code as dead without concrete proof of zero references across the repository.
* **Audit first, ask always**: Never delete or rewrite code during the scan. Always present findings and wait for user confirmation.
* **Verify after cleanup**: Always run syntax checks (`node --check`, `python -m py_compile`, `cargo check`) after deletions.

---

## License
MIT License. Copyright (c) 2026 itszshinnn.
