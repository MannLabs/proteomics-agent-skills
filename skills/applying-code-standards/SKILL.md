---
name: applying-code-standards
description: |
  Apply code quality standards for scientific data analysis. ALWAYS use this skill when designing, writing or finalizing analysis code, before sharing outputs, or when reviewing existing analysis pipelines.
---

# Applying Code Standards

Review checklist for ensuring analysis code meets quality standards for maintainability and reproducibility.
Covers project structure, readability, reproducibility, and documentation.

---

## Required Output Files (MANDATORY)

Your analysis MUST include these files. Do not consider the analysis complete without them:

1. **`config.py`** - All parameters with rationale comments explaining each threshold
2. **`requirements.txt`** - Pinned dependency versions (e.g., `pandas==2.0.3`)
3. **`README.md`** - Setup and usage instructions

---

## Checklist

Copy this checklist and track progress:

```
Code Quality Review:
- [ ] Project structure
- [ ] Readability
- [ ] No unnecessary duplication
- [ ] Configuration is explicit
- [ ] Inputs are validated
- [ ] Reproducibility
- [ ] Documentation
- [ ] Housekeeping
```

---

## Guidelines

### 1. Project Structure

- ALWAYS maintain a clean, consistent directory layout
- Separate exploratory work (notebooks, helper scripts) from production code (modules)
- Treat raw data as immutable — never overwrite originals
- ALWAYS create `requirements.txt` with the python version (`echo "# "$(python --version) >> requirements.txt`) and pinned versions (`pip freeze >> requirements.txt`) to capture the software environment for reproducibility.

**Required: `requirements.txt` example:**
```
# Python 3.13.2
pandas==2.0.3
numpy==1.24.0
```

### 2. Readability

- A program should not require readers to hold more than a handful of facts in memory at once
- Make names consistent, distinctive, and meaningful (functions, variables, files)
- Make code style and formatting consistent
- Keep functions small and focused — each should do one thing well
- Do not comment and uncomment sections of code to control behavior; use configuration instead
- Avoid hardcoded paths: use pathlib and relative paths or environment variables
- Keep data loading, processing, analysis, and visualization in distinct stages

### 3. No Unnecessary Duplication

- Every piece of data must have a single authoritative representation
- Modularize code rather than copying and pasting
- Re-use well-maintained libraries instead of reimplementing common functionality
- Prefer a little duplication over the wrong abstraction — do not over-engineer

### 4. Explicit Configuration (REQUIRED)

- ALWAYS define experimental parameters (thresholds, hyperparameters) as named constants in one place
- ALWAYS document the rationale for cutoffs and thresholds near their definitions
- ALWAYS separate configuration from code using a config file

**Required: `config.py` example with rationale:**
```python
# Analysis Configuration

# FDR threshold for multiple testing correction
# Rationale: Standard proteomics threshold balancing discovery vs false positives
FDR_THRESHOLD = ...

# Minimum valid values required per group for statistical testing
# Rationale: At least ... values needed for variance estimation
MIN_VALID_VALUES = ...

```

### 5. Input Validation

- Validate inputs early using assertions or schema libraries (pandera, pydantic)
- Add assertions at key checkpoints to verify intermediate results
- Fail fast and loudly — silent failures hide bugs

### 6. Reproducibility

- ALWAYS set random seeds explicitly via named variables in config
- ALWAYS record the software environment in `requirements.txt`
- Automate the full pipeline so results can be regenerated from raw data following a linear analysis path
- Log or save key parameters and metadata alongside outputs

### 7. Documentation

- Aim for self-explanatory code; reserve comments for *why*, not *what*
- ALWAYS add type hints for ALL function signatures
- Place a brief docstring at the top of every module explaining its purpose
- ALWAYS include a `README.md` explaining setup and how to run the analysis

**Required: Type hints for all functions:**
```python
def divide(enumerator: float, divisor: float) -> float:
    """Divide enumerator by divisor"""
    return  enumerator/divisor
```

**Required: `README.md` template:**
```markdown
# Analysis: [Name]

## Overview
Brief description of the analysis purpose.

## Setup
```bash
pip install -r requirements.txt
```

## Usage
```bash
python main.py
```

## Input
- `data/input_file.txt` - Description of input data

## Output
- `output/results.csv` - Description of output

## Configuration
Parameters can be modified in `config.py`.
```

### 8. Housekeeping

- Remove unused code, temporary files, and dead notebooks at the end

---

## Pre-Submission Checklist (MANDATORY)

Before finalizing your analysis, verify ALL of these exist:

- [ ] **`config.py`** exists with ALL thresholds and rationale comments
- [ ] **`requirements.txt`** exists with pinned versions (e.g., `pandas==2.0.3`)
- [ ] **`README.md`** exists with setup/usage/input/output sections
- [ ] All functions have type hints (e.g., `def func(x: int) -> str:`)

If any item is unchecked, go back and add it before completing the analysis.
