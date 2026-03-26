# GCM - Git Checkout Main

[![PR Check](https://github.com/jewzaam/git-checkout-main/actions/workflows/pr-check.yml/badge.svg)](https://github.com/jewzaam/git-checkout-main/actions/workflows/pr-check.yml)
[![Coverage](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/jewzaam/git-checkout-main/main/.github/badges/coverage.json)](https://github.com/jewzaam/git-checkout-main/actions/workflows/coverage-badge.yml)

A portable Python rewrite of the original git workflow automation tool for managing the "checkout main" workflow with proper fork management and branch cleanup.

## Features

- **Portable Configuration**: No hardcoded usernames, remotes, or branch names
- **Multi-Provider Support**: Works with GitHub, GitLab, and other git providers
- **Fork Management**: Automatically manages user forks and upstream repositories
- **Branch Cleanup**: Removes merged and gone branches from local and remote repositories

- **Safety Features**: Dry-run mode and confirmation prompts for destructive operations
- **Parallel Operations**: Optimized performance with concurrent git operations

## Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd gcm
```

2. Set up the development environment:
```bash
make requirements
```

## Configuration

Create a configuration file at one of these locations:
- `.gcm.yaml` (local repository)
- `~/.gcm.yaml` (user home)
- `~/.config/gcm.yaml` (XDG config)

Example configuration:
```yaml
providers:
  github:
    username: "your-github-username"
    fork_remote: "your-github-username"
  gitlab:
    username: "your-gitlab-username"
    fork_remote: "your-gitlab-username"

behavior:
  confirm_destructive: true
  parallel_operations: true
```

See `.gcm.yaml.example` for a complete configuration template.

## Usage

Basic usage:
```bash
./gcm.py
```

Create missing fork remotes:
```bash
./gcm.py -m
```

Dry-run mode (show what would be done):
```bash
./gcm.py --dry-run
```

Custom configuration file:
```bash
./gcm.py --config /path/to/config.yaml
```

## What GCM Does

1. **Detects Repository Info**: Automatically identifies the trunk branch and git provider

3. **Sets Up Remotes**: Creates fork remotes if requested with `-m` flag
4. **Configures Push URLs**: Disables push to upstream when forks are detected
5. **Updates Trunk**: Checks out and pulls latest changes from the trunk branch
6. **Prunes Remotes**: Removes stale remote references
7. **Cleans Branches**: Deletes merged and gone branches from local and remotes
8. **Syncs Forks**: Pushes updated trunk to configured user forks

## Development

### Running Tests

The project includes both unit and integration tests to ensure reliability.

#### Unit Tests
Fast, isolated tests that mock external dependencies:

```bash
make test-unit
# or simply:
make test
```

#### Integration Tests  
End-to-end tests using real git repositories in isolated environments:

```bash
make test-integration
```

#### All Tests
Run both unit and integration tests:

```bash
make test-all
```

#### Coverage
Run tests with coverage reporting:

```bash
make coverage
```

#### Test Documentation
- **Unit Test Plan**: `docs/UNIT_TEST_PLAN.md` - Details on unit test coverage and categories
- **Integration Test Plan**: `docs/INTEGRATION_TEST_PLAN.md` - Comprehensive integration test scenarios and implementation

### Code Quality

Lint and format code:
```bash
make lint
```

### Build Tasks

See all available tasks:
```bash
make help
```

Clean build artifacts:
```bash
make clean
```

## Requirements

See [docs/REQUIREMENTS.md](docs/REQUIREMENTS.md) for detailed requirements and edge cases.

## Test Plan

See [docs/TEST_PLAN.md](docs/TEST_PLAN.md) for the complete unit test plan.

## Migration from Original Script

The original bash script has been preserved in `reference/gcm`. Key improvements in the Python version:

- **Configurable**: No hardcoded usernames or provider-specific logic
- **Error Handling**: Comprehensive error handling with helpful messages
- **Testing**: Full unit test coverage with clear test cases
- **Logging**: Detailed logging for debugging and monitoring
- **Dry-Run**: Preview changes before execution
- **Documentation**: Clear requirements and usage documentation

## Contributing

1. Follow the test-driven development approach outlined in the test plan
2. Ensure all tests pass: `make test`
3. Format code: `make format`
4. Run linting: `make lint`
5. Add attribution: "Assisted-by: Cursor (Claude Sonnet 4)" to commits
