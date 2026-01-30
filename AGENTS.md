# AGENTS.md - Development Guide for GEAS-AI

**Purpose:** Essential commands and code guidelines for AI agents working on GEAS-AI repository.

---

## 🚀 Build/Lint/Test Commands

### Environment Setup
```bash
# Install dependencies
uv sync

# Install pre-commit hooks
uv run pre-commit install
```

### Testing Commands
```bash
# Run all tests
uv run pytest

# Run single test file
uv run pytest tests/core/test_bolt.py

# Run specific test function
uv run pytest tests/core/test_bolt.py::test_bolt_creation

# Run tests with coverage
uv run pytest --cov=src

# Run tests with verbose output
uv run pytest -v
```

### Code Quality Commands
```bash
# Lint code (ruff)
uv run ruff check .

# Auto-fix linting issues
uv run ruff check . --fix

# Check formatting
uv run ruff format --check .

# Apply formatting
uv run ruff format .

# Type checking (mypy - strict mode)
uv run mypy src

# Run all pre-commit hooks manually
uv run pre-commit run --all-files
```

### CI Validation
All commands above run automatically in GitHub Actions on push/PR to main.

---

## 📝 Code Style Guidelines

### Type Hints (MANDATORY - Strict MyPy)
```python
def compute_sha256(file_path: Path) -> str:
    """Computes SHA256 hash of the file content (normalized)."""
    # Implementation...

def new(name: str) -> None:
    """Start a new GEAS Unit of Work (Bolt)."""
    # Implementation...
```

### Import Organization
```python
# 1. Standard library
import os
import re
from pathlib import Path

# 2. Third-party
import typer
from rich.console import Console

# 3. Local imports
from geas_ai import utils
from geas_ai.bolt import Bolt
```

### Naming Conventions
```python
# Variables/Functions: snake_case
bolt_path = get_active_bolt_path()
current_state = "draft"

# Classes: PascalCase
class BoltManager:
    pass

# Constants: SCREAMING_SNAKE_CASE
DEFAULT_AGENTS_YAML = "agents.yaml"
MAX_FILE_SIZE = 1024 * 1024

# Files: snake_case.py
# lifecycle.py, identity.py, utils.py
# Test files: test_<module>.py
# test_bolt.py, test_lifecycle.py
```

### Docstrings (Google Style - MANDATORY)
```python
def validate_slug(name: str) -> str:
    """Validates that the name contains only alphanumeric characters, hyphens, or underscores.
    
    Args:
        name: The bolt name to validate.
        
    Returns:
        The validated name.
        
    Raises:
        typer.BadParameter: If the name contains invalid characters.
        
    Usage:
        >>> validate_slug("feature-login")
        'feature-login'
    """
    # Implementation...
```

### Error Handling Pattern
```python
from rich.console import Console
import typer

console = Console()

def some_function():
    if not expected_condition:
        console.print("[bold red]Error:[/bold red] Something went wrong.")
        raise typer.Exit(code=1)
```

### CLI Command Development
```python
import typer
from geas_ai import utils

def my_command(name: str) -> None:
    """Command description.
    
    Args:
        name: Parameter description.
        
    Usage:
        $ geas my-command feature-name
    """
    utils.ensure_geas_root()  # Always validate GEAS environment
    # Implementation...
```

---

## 🏗️ Development Workflow

### Directory Structure
```
src/geas_ai/
├── commands/          # CLI command implementations
├── core/             # Core business logic
├── utils/            # Shared utility functions
└── main.py           # CLI entry point

tests/                 # Test suite (mirrors src structure)
├── commands/         
├── core/            
└── conftest.py       # Common test fixtures
```

### Key Utility Functions
```python
from geas_ai.utils import (
    get_geas_root,           # Path to .geas directory
    ensure_geas_root,        # Validate GEAS is initialized
    validate_slug,          # Validate bolt name format
    compute_sha256,         # Compute file hash
    get_active_bolt_path,   # Current bolt directory
    get_active_bolt_name,   # Current bolt name
)
```

### Testing Approach
```python
# Use fixtures from conftest.py
def test_my_function(setup_geas_environment):
    """Test function with temporary GEAS environment."""
    # Test implementation...

# CLI testing with CliRunner
def test_command(runner, setup_geas_environment):
    result = runner.invoke(app, ["my-command"])
    assert result.exit_code == 0
```

### GEAS Protocol Workflow
```
1. geas new <bolt-name>       # Create workspace
2. Edit 01_request.md → geas seal req
3. Create 02_specs.md → geas seal specs  
4. Create 03_plan.md → geas seal plan
5. Implement in src/ (mutable phase)
6. geas prove                   # Run tests & generate manifest
7. geas seal mrp                # Seal Merge Request Package
8. geas verify                  # Verify integrity
9. geas approve                 # Approve for merge
```

---

## ✅ Quality Checklist

### Before Submitting Code
- [ ] All functions have complete type hints
- [ ] All public functions have Google-style docstrings
- [ ] `uv run pytest` passes (target >85% coverage)
- [ ] `uv run ruff check .` passes
- [ ] `uv run ruff format --check .` passes
- [ ] `uv run mypy src` passes
- [ ] No repeated logic (DRY principle)
- [ ] Error handling uses `typer.Exit(code=1)`

### GEAS Protocol Compliance
- [ ] Read `.geas/active_context.md` for current bolt
- [ ] Follow seal sequence: `req` → `specs` → `plan` → `mrp`
- [ ] Do not edit code until `03_plan.md` is sealed (if strict GEAS)
- [ ] Use utility functions from `geas_ai.utils`
- [ ] Validate with `ensure_geas_root()` in commands

### Testing Requirements
- [ ] Write tests before implementation (TDD)
- [ ] Use `setup_geas_environment` fixture
- [ ] Cover happy paths, edge cases, and failures
- [ ] Use `CliRunner` for CLI command tests
- [ ] Follow naming: `test_<function_name>`

---

## 🎯 Quick Reference

```bash
# Development cycle
uv sync                    # Setup
uv run pytest             # Test  
uv run ruff check .        # Lint
uv run mypy src           # Type check

# GEAS commands
geas new feature-name     # Start bolt
geas status               # Check status
geas prove                # Generate evidence
geas verify               # Validate integrity
```

**Critical:** This repository enforces strict type checking, comprehensive testing, and follows the GEAS cryptographic governance protocol. Always validate GEAS environment and respect the seal sequence.