---
name: deadcode
description: Universal dead code, unused logic, and redundancy auditor across any programming language. Scans repositories for uncalled functions, unreachable branches, unused imports, duplicate rules, and inactive commented-out code. Triggers with /deadcode.
license: MIT
metadata:
  author: itszshinnn
---

# Universal Dead Code Auditor (deadcode)

Scan, identify, and safely eliminate dead code, unused functions, unreachable branches, duplicate declarations, and abandoned scaffolding across any programming language.

## Core Philosophy
1. **Evidence before accusation:** Never claim code is dead based on intuition. Always prove zero call sites or unreachable conditions with concrete evidence across the entire codebase.
2. **Audit first, ask always:** Never delete, refactor, or edit code during the initial scan. Present an itemized audit report with line numbers and reasons, then wait for explicit confirmation.
3. **Verify after cleanup:** Every cleanup must conclude with a build, syntax, or test verification step to ensure zero regressions.

---

## Supported Languages & Native Tooling

When triggered, detect the project stack and leverage native tools if installed:

| Language / Stack | Native Linter / Tool (If Available) | Fallback Static Analysis |
| :--- | :--- | :--- |
| **JavaScript / TypeScript** | `eslint`, `ts-prune`, `knip`, `depcheck` | Static declaration-to-call-site search across repo |
| **Python** | `vulture`, `flake8`, `ruff`, `autoflake` | AST / regex search for unreferenced defs & imports |
| **HTML / CSS** | `purgecss`, `stylelint` | Selector-to-DOM matching, duplicate media query checks |
| **Rust** | `cargo check`, `cargo-udeps` | Unused warning audit from compiler |
| **Go** | `go vet`, `deadcode`, `golangci-lint` | Go compiler dead code analysis |
| **Java / Kotlin** | `spotbugs`, `pmd` | Static symbol reference check |
| **C / C++** | `cppcheck`, `clang-tidy` | Header & translation unit reference analysis |
| **PHP / Ruby** | `phpstan`, `rubocop` | Method symbol lookup |

If no linter or compiler is configured (e.g. vanilla web projects or lightweight scripts), execute the **Fallback Static Analysis** procedure directly.

---

## The 4-Phase Audit Procedure

### Phase 1: Stack & Architecture Reconnaissance
1. Inspect the root directory to identify languages, package managers, and configuration files (`package.json`, `requirements.txt`, `pyproject.toml`, `Cargo.toml`, `go.mod`, etc.).
2. Map the active entry points (e.g., `index.html`, `main.py`, `src/index.ts`, `main.go`).
3. Note any public API exports, plugin hooks, or dynamically invoked symbols that should not be flagged as dead.

### Phase 2: Static Analysis & Cross-Referencing
Search for the 5 categories of dead code:

1. **Uncalled Definitions:**
   - Functions, methods, classes, and structs declared in local files that have 0 references across all files.
   - Ignore framework lifecycle methods (e.g., `DOMContentLoaded`, `onMounted`, `componentDidMount`, `__init__`, `main`).
2. **Unreachable Logic:**
   - Statements positioned directly after `return`, `throw`, `break`, `continue`, or `exit()`.
   - Hardcoded false guards like `if (false)`, `if (0)`, `if False:`, `if 0:` that wrap dead blocks.
3. **Unused Imports & Dependencies:**
   - Imported libraries, modules, or header files that are never called in the file body.
4. **Duplicate & Fragmented Declarations:**
   - In CSS: Multiple identical `@media` queries, duplicate class selectors, or redundant property overrides.
   - In JS/Python/Go: Duplicate function declarations where the second silently overwrites the first.
5. **Abandoned Commented-Out Code:**
   - Large blocks of code commented out with `/* ... */`, `//`, or `#` that have been replaced by newer implementations.

### Phase 3: Structured Audit Report
Present findings to the user using this standard format:

```markdown
### Project Stack Detected
* **Environment:** [Detected Stack, e.g. Vanilla JavaScript, HTML5, CSS3 / Python 3.12 / Rust]
* **Files scanned:** [Number of files checked]

---

### Audit Report: [Count] Items Found

#### 1. [Category: Uncalled Function / Unreachable Logic / Duplicate Rule / etc.]
* **File:** `[Path]` (Lines [X] to [Y])
* **Code:** `[Brief 1-line signature or snippet]`
* **Finding:** [Specific reason why it is dead, e.g. "Zero references found across the repository."]
* **Risk level:** [Zero risk / Low risk / Verification recommended]

---

### Summary of Impact
* **Total dead lines identified:** [Number] lines
* **Impact:** [Cleanliness, build speed, file size reduction]

### Next Step
Would you like me to remove all [Count] items, or would you like to keep specific ones?
```

### Phase 4: Execution & Verification (Only After Approval)
1. Apply minimal, surgical deletions to remove only the approved items.
2. Avoid touching adjacent working code or modifying styling outside the target blocks.
3. Run the language syntax check or compiler command (e.g., `node --check`, `python -m py_compile`, `cargo check`, `go vet`).
4. Present a final clean summary of deleted lines and confirm verified compilation.
