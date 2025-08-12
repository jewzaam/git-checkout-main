# GCM - Git Checkout Master

Git Checkout Master (GCM) is a portable Python git workflow automation tool that manages the "checkout master" workflow with proper fork management and branch cleanup.

Always reference these instructions first and fallback to search or bash commands only when you encounter unexpected information that does not match the info here.

## Working Effectively

### Bootstrap and Build
Run these commands in sequence to set up a working development environment:

- `make venv` -- creates Python virtual environment (takes 3 seconds)
- `make uv` -- installs uv package manager (takes 2 seconds)
- `make requirements-dev` -- installs all dependencies (takes 5 seconds)

**FALLBACK**: If `make uv` or `make requirements-dev` fails due to network issues:
- Use direct pip installation: `.venv/bin/pip install -r requirements-dev.txt`
- Network timeouts with PyPI are common in restricted environments

### Testing
All test commands are fast and reliable:

- `make test-unit` -- runs 43 unit tests (takes 1 second). NEVER CANCEL.
- `make test-integration` -- runs 12 integration tests (takes 2 seconds). NEVER CANCEL.  
- `make test-all` -- runs all 55 tests (takes 2 seconds). NEVER CANCEL.
- `make coverage` -- runs tests with coverage report (takes 2.5 seconds). NEVER CANCEL.

**FALLBACK**: If make targets fail, use direct commands:
- `.venv/bin/python -m pytest tests/unit/ -v`
- `.venv/bin/python -m pytest tests/integration/ -v`
- `.venv/bin/python -m pytest tests/ -v`
- `.venv/bin/python -m pytest tests/ --cov=gcm --cov-report=html --cov-report=term`

### Code Quality
- `make lint` -- runs ruff formatter and linter (takes 0.3 seconds)

**FALLBACK**: If make targets fail, use direct commands:
- `.venv/bin/python -m ruff format gcm.py tests/`
- `.venv/bin/python -m ruff check --fix gcm.py tests/`

### Running the Application
- `./gcm.py --help` -- shows usage information
- `./gcm.py --dry-run` -- shows what would be done without making changes
- `./gcm.py -m --dry-run` -- dry-run with fork remote creation
- `./gcm.py --config /path/to/config.yaml` -- use custom configuration file

## Validation

### Required Validation Steps
ALWAYS run these validation steps after making any code changes:

1. `make lint` -- must pass with no errors
2. `make test-unit` -- all 43 unit tests must pass  
3. `make test-integration` -- all 12 integration tests must pass
4. `./gcm.py --help` -- must display help without errors
5. `./gcm.py --dry-run` -- should run (may fail with "Could not determine trunk branch" if not in a proper git repo, which is expected)

### Manual Testing Scenarios
After making changes to core functionality, test these scenarios:

- **Configuration loading**: Test with `./gcm.py --config .gcm.yaml.example --dry-run`
- **Help system**: `./gcm.py --help` should show all options
- **Dry-run mode**: `./gcm.py --dry-run` should execute without making changes (will fail with "Could not determine trunk branch" if not in a proper git repo, which is expected)
- **Invalid config**: Test with malformed YAML to ensure proper error handling (creates helpful error messages)

### Complete Validation Sequence
Run this complete validation after any code changes:
```bash
# 1. Format and lint
.venv/bin/python -m ruff format gcm.py tests/
.venv/bin/python -m ruff check --fix gcm.py tests/

# 2. Run all tests
.venv/bin/python -m pytest tests/unit/ -q
.venv/bin/python -m pytest tests/integration/ -q

# 3. Test application functionality  
./gcm.py --help > /dev/null
./gcm.py --dry-run 2>&1 | grep -q "Could not determine trunk branch"
```

### Build Timing and Timeouts
- **Environment setup**: Total ~10 seconds for complete setup
- **Dependencies**: 5 seconds maximum for `make requirements-dev`
- **All tests**: Under 3 seconds total - NEVER CANCEL test commands
- **Linting**: Under 1 second - NEVER CANCEL

## Common Tasks

### Repo Structure Overview
```
/home/runner/work/git-checkout-main/git-checkout-main/
├── .gcm.yaml.example       # Configuration template
├── gcm.py                  # Main executable script (325 lines)
├── Makefile               # Main build file (includes modular makefiles)
├── make/                  # Modular Makefiles
│   ├── env.mk            # Environment and dependency management
│   ├── test.mk           # Test targets
│   └── lint.mk           # Code quality targets
├── requirements.txt       # Runtime dependencies (PyYAML only)
├── requirements-dev.txt   # Development dependencies (pytest, ruff, coverage)
├── tests/                # Test directory
│   ├── unit/             # Unit tests (43 tests)
│   ├── integration/      # Integration tests (12 tests)
│   └── helpers/          # Test utilities
├── docs/                 # Documentation
└── reference/            # Original implementation reference
```

### Key Dependencies
- **Python 3.8+** required
- **PyYAML>=6.0** for configuration
- **pytest>=7.0.0** for testing
- **ruff>=0.1.0** for linting/formatting
- **pytest-cov>=4.0.0** for coverage

### Configuration
The tool supports configuration via:
- **CLI argument**: `--config /path/to/config.yaml` (recommended for testing)
- **Auto-discovery** in this order:
  1. `.gcm.yaml` (local repository - **not recommended**, now in `.gitignore`)
  2. `~/.gcm.yaml` (user home)  
  3. `~/.config/gcm.yaml` (XDG config)

Use `.gcm.yaml.example` as a template. For development/testing, use `--config .gcm.yaml.example` to avoid creating local config files.

### Make Targets Reference
Run `make help` to see all available targets:

- `make venv` -- Create Python virtual environment
- `make uv` -- Install uv package manager  
- `make requirements` -- Install runtime dependencies only
- `make requirements-dev` -- Install all dependencies (runtime + dev/test)
- `make test` -- Run unit tests (default test target)
- `make test-unit` -- Run unit tests only
- `make test-integration` -- Run integration tests only
- `make test-all` -- Run all tests
- `make coverage` -- Run tests with coverage report
- `make lint` -- Run linting and auto-format code
- `make clean` -- Remove temporary and backup files
- `make help` -- Display help message

### Error Handling Notes
- The tool may fail with "Could not determine trunk branch" when run outside a proper git repository (expected behavior)
- Configuration errors are properly caught and reported with helpful messages
- All git operations include comprehensive error handling
- Network failures are handled gracefully with informative error messages

### Development Notes
- The codebase has 64% test coverage across 325 lines of code
- All commits should include: "Assisted-by: <Tool> (<Model>)" attribution
- New files should include: "Generated By: <Tool> (<Model>)" as a comment
- Code is automatically formatted with ruff during linting
- Both unit and integration tests use pytest with comprehensive mocking

## Architecture
- **Single-file design**: Main logic in `gcm.py` for portability
- **Modular Makefiles**: Build logic split across `make/*.mk` files
- **Comprehensive testing**: Separate unit and integration test suites
- **Configuration-driven**: YAML-based configuration system
- **Error-resilient**: Extensive error handling and dry-run support