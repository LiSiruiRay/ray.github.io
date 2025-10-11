---
title: Git configs for a python project
date: 2025-06-22 20:30:56
tags:
---


Description: Meta-documentation for project workflow, Git setup, and code quality tools

# Meta Documentation - Project Workflow & Git Setup

This document describes the complete development workflow, Git configuration, and code quality tools used in the EQUO project. Use this as a blueprint for setting up similar Python projects with UV package management.

## 🛠️ Technology Stack

### Core Tools
- **Python**: 3.11+
- **Package Manager**: [UV](https://github.com/astral-sh/uv) - Fast Python dependency management
- **Web Framework**: Flask 3.0+
- **AI Framework**: LangGraph + LangChain + OpenAI
- **Data Validation**: Pydantic
- **Testing**: Pytest with coverage

### Code Quality Tools
- **Formatter**: Black (code formatting)
- **Import Sorter**: isort (import organization)
- **Linter**: flake8 (code style and error checking)
- **Type Checker**: mypy (static type analysis) - currently disabled
- **Pre-commit**: Automated quality checks before commits

## 📁 Configuration Files

### `.flake8` - Linting Configuration
```ini
[flake8]
max-line-length = 120
extend-ignore = E203, W503
```

**Key Settings**:
- **Line Length**: 120 characters (compatible with Black's default 88 + buffer)
- **Ignored Rules**:
  - `E203`: Whitespace before ':' (conflicts with Black)
  - `W503`: Line break before binary operator (conflicts with Black)

### `.pre-commit-config.yaml` - Pre-commit Hooks
```yaml
repos:
  # Standard hooks for file quality
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace      # Remove trailing spaces
      - id: end-of-file-fixer       # Ensure files end with newline
      - id: check-yaml              # Validate YAML files
      - id: check-added-large-files # Prevent large file commits
      - id: check-json              # Validate JSON files
      - id: check-toml              # Validate TOML files
      - id: check-merge-conflict    # Detect merge conflict markers
      - id: debug-statements        # Detect debug print statements

  # Code formatting
  - repo: https://github.com/psf/black
    rev: 23.12.1
    hooks:
      - id: black
        language_version: python3

  # Import sorting
  - repo: https://github.com/pycqa/isort
    rev: 5.13.2
    hooks:
      - id: isort

  # Linting
  - repo: https://github.com/pycqa/flake8
    rev: 7.0.0
    hooks:
      - id: flake8
        args: ["--max-line-length=120", "--extend-ignore=E203,W503"]

  # Custom local hooks
  - repo: local
    hooks:
      # Run tests before commit
      - id: pytest
        name: pytest
        entry: uv run pytest
        language: system
        pass_filenames: false
        always_run: true
        stages: [pre-commit]

      # Enforce conventional commit messages
      - id: commit-msg-format
        name: Conventional Commit Format
        entry: python
        language: system
        args:
          - -c
          - |
            import re
            import sys
            if len(sys.argv) < 2:
                sys.exit(0)
            with open(sys.argv[1], 'r') as f:
                commit_msg = f.read().strip()
            pattern = r"^(feat|fix|chore|docs|style|refactor|test|perf|ci|build|revert)(\(.+\))?: .+"
            if not re.match(pattern, commit_msg):
                print(f"❌ Commit message format is invalid!")
                print(f"📋 Current message: {commit_msg}")
                print(f"✅ Expected format: type(scope): description")
                print(f"📝 Valid types: feat, fix, chore, docs, style, refactor, test, perf, ci, build, revert")
                print(f"📝 Examples:")
                print(f"   feat: add user authentication")
                print(f"   fix(api): resolve login endpoint error")
                print(f"   docs: update README.md")
                sys.exit(1)
        stages: [commit-msg]
        pass_filenames: false
```

## 🔄 Development Workflow

### Initial Project Setup

1. **Create project structure**:
```bash
mkdir new-project && cd new-project
git init
```

2. **Set up UV and dependencies**:
```bash
# Initialize UV project
uv init --package

# Add core dependencies
uv add flask pydantic pytest pytest-cov

# Add development dependencies
uv add --dev black isort flake8 pre-commit mypy
```

3. **Copy configuration files**:
```bash
# Copy these files from EQUO project
cp .flake8 .pre-commit-config.yaml new-project/
```

4. **Install pre-commit hooks**:
```bash
uv run pre-commit install
uv run pre-commit install --hook-type commit-msg
```

### Daily Development Commands

```bash
# Install/update dependencies
uv sync --dev

# Run tests
uv run pytest
uv run pytest --cov=src --cov-report=html

# Code quality checks
uv run black .
uv run isort .
uv run flake8 src tests
uv run pre-commit run --all-files

# Development server
uv run python -m src.your_package.api.app
```

### Git Workflow

#### Conventional Commit Format
All commits must follow the conventional commit format:

**Pattern**: `type(scope): description`

**Valid Types**:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, no logic change)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks
- `perf`: Performance improvements
- `ci`: CI/CD changes
- `build`: Build system changes
- `revert`: Reverting previous commits

**Examples**:
```bash
git commit -m "feat: add user authentication"
git commit -m "fix(api): resolve login endpoint error"
git commit -m "docs: update README.md"
git commit -m "test: add unit tests for user service"
git commit -m "refactor(agent): improve error handling"
```

#### Pre-commit Workflow
Every commit automatically runs:

1. **File Quality Checks**:
   - Remove trailing whitespace
   - Fix end-of-file formatting
   - Validate YAML/JSON/TOML files
   - Check for large files and merge conflicts

2. **Code Quality**:
   - Black formatting
   - isort import sorting
   - flake8 linting

3. **Testing**:
   - Full pytest suite must pass

4. **Commit Message**:
   - Validates conventional commit format

**Example commit process**:
```bash
# Make changes
git add .

# Commit (triggers all pre-commit hooks)
git commit -m "feat: add new financial analysis feature"

# If any hooks fail, fix issues and recommit
# Hooks run automatically on every commit attempt
```

## 🧪 Testing Configuration

### Pytest Setup
**File**: `pyproject.toml`
```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]
addopts = [
    "--strict-markers",
    "--strict-config",
    "--verbose",
]
```

### Coverage Configuration
```toml
[tool.coverage.run]
source = ["src"]
omit = [
    "*/tests/*",
    "*/test_*",
    "*/__pycache__/*",
    "*/site-packages/*",
]

[tool.coverage.report]
exclude_lines = [
    "pragma: no cover",
    "def __repr__",
    "raise AssertionError",
    "raise NotImplementedError",
]
```

## 📦 Package Management with UV

### Core UV Commands
```bash
# Project initialization
uv init --package                    # Create new package project
uv init                             # Create application project

# Dependency management
uv add package-name                 # Add production dependency
uv add --dev package-name           # Add development dependency
uv remove package-name              # Remove dependency
uv sync                             # Install all dependencies
uv sync --dev                       # Install with dev dependencies

# Environment management
uv venv                             # Create virtual environment
uv pip list                         # List installed packages
uv pip show package-name            # Show package info

# Running commands
uv run python script.py             # Run Python script
uv run pytest                       # Run tests
uv run black .                      # Run formatter
```

### UV Project Structure
```toml
# pyproject.toml
[project]
name = "your-project"
version = "0.1.0"
description = "Project description"
authors = [{name = "Your Name", email = "you@example.com"}]
requires-python = ">=3.11"
dependencies = [
    "flask>=3.0.0",
    "pydantic>=2.0.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.0.0",
    "pytest-cov>=4.0.0",
    "black>=23.0.0",
    "isort>=5.12.0",
    "flake8>=6.0.0",
    "pre-commit>=3.0.0",
]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.black]
line-length = 88
target-version = ['py311']

[tool.isort]
profile = "black"
```

## 🚀 Project Template Checklist

To replicate this workflow in a new project:

### ✅ Essential Files to Copy
- [ ] `.flake8` - Linting configuration
- [ ] `.pre-commit-config.yaml` - Pre-commit hooks
- [ ] `pyproject.toml` sections for tools (black, isort, pytest, coverage)

### ✅ Setup Commands
```bash
# 1. Initialize project
uv init --package
cd your-project

# 2. Copy configuration files
cp /path/to/equo/.flake8 .
cp /path/to/equo/.pre-commit-config.yaml .

# 3. Install dependencies
uv add flask pydantic
uv add --dev pytest pytest-cov black isort flake8 pre-commit

# 4. Set up pre-commit
uv run pre-commit install
uv run pre-commit install --hook-type commit-msg

# 5. Test the workflow
uv run pre-commit run --all-files
```

### ✅ Development Environment
- [ ] Python 3.11+
- [ ] UV installed
- [ ] Git repository initialized
- [ ] OpenAI API key (if using AI features)
- [ ] Environment file created from template

## 🔧 Troubleshooting

### Common Issues

#### **Pre-commit Hook Failures**
```bash
# If hooks fail, fix issues and recommit
git add .
git commit -m "fix: resolve code quality issues"

# Skip hooks in emergency (not recommended)
git commit --no-verify -m "emergency: skip pre-commit hooks"
```

#### **Flake8 Line Length Issues**
- **Solution**: Use Black formatting (88 chars) + flake8 config (120 chars max)
- **Fix**: Run `uv run black .` before committing

#### **Import Sorting Issues**
- **Solution**: Run `uv run isort .` to fix import order
- **Configuration**: isort uses "black" profile for compatibility

#### **Test Failures**
- **Solution**: Fix failing tests before committing
- **Bypass**: Use `--no-verify` flag (not recommended)

### Environment Issues
```bash
# Recreate UV environment
uv venv --force
uv sync --dev

# Update all dependencies
uv sync --upgrade

# Check environment
uv pip list
```

## 📋 Quality Standards

This workflow enforces:

- **Code Formatting**: Consistent style with Black
- **Import Organization**: Clean imports with isort
- **Code Quality**: Linting with flake8
- **Type Safety**: Type hints required (mypy ready)
- **Test Coverage**: All tests must pass before commit
- **Commit Messages**: Conventional commit format
- **File Quality**: No trailing whitespace, proper line endings

## 🔗 Related Tools

- **[UV Documentation](https://github.com/astral-sh/uv)** - Fast Python package manager
- **[Pre-commit](https://pre-commit.com/)** - Git hooks framework
- **[Black](https://black.readthedocs.io/)** - Python code formatter
- **[flake8](https://flake8.pycqa.org/)** - Python linter
- **[Conventional Commits](https://www.conventionalcommits.org/)** - Commit message standard

---

**🎯 Result**: A robust, consistent development workflow that enforces code quality, testing, and proper Git practices across all team members and projects.
