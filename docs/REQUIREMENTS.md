# GCM (Git Checkout Master) - Requirements

## Overview

GCM is a git workflow automation tool that manages the "checkout master" workflow for maintaining clean local repositories with proper fork management and branch cleanup.

## Core Requirements

### 1. Trunk Branch Management
- **REQ-001**: Automatically detect the primary/trunk branch from `origin/HEAD`
- **REQ-002**: Support common trunk branch names: `main`, `master`, `trunk`, or any branch pointed to by `origin/HEAD`
- **REQ-003**: Checkout and pull latest changes from trunk branch
- **REQ-004**: Handle cases where trunk branch doesn't exist locally (create from origin)

### 2. Repository Detection and Management
- **REQ-005**: Automatically detect git hosting provider (GitHub, GitLab, or others)
- **REQ-006**: Extract repository information from git remotes
- **REQ-007**: Support multiple git hosting providers simultaneously

### 3. Fork Management
- **REQ-008**: Detect existing user forks by configurable remote names
- **REQ-009**: Optionally create missing forks when requested (make remotes mode)
- **REQ-010**: Disable push to upstream origin when forks are present
- **REQ-011**: Sync trunk branch to user forks after updates

### 4. Branch Cleanup
- **REQ-012**: Delete local branches that have been merged to trunk
- **REQ-013**: Delete local branches where remote tracking branch is gone
- **REQ-014**: Delete remote fork branches that have been merged to trunk
- **REQ-015**: Prune stale remote references from all remotes

### 5. Error Handling and Recovery
- **REQ-016**: Offer reset/clean option when checkout or pull fails
- **REQ-017**: Warn user about destructive operations (hard reset, clean)
- **REQ-018**: Require explicit confirmation for destructive operations
- **REQ-019**: Graceful handling of network issues or remote access problems

### 6. VPN Integration
- **REQ-020**: Support optional VPN connection for specific providers
- **REQ-021**: Make VPN command configurable per provider

### 7. Configuration Management
- **REQ-022**: Support configuration file for user-specific settings
- **REQ-023**: Make all hardcoded values configurable:
  - Username mappings per provider
  - Remote names for forks
  - VPN commands per provider
  - Custom branch naming patterns
- **REQ-024**: Support both global and per-repository configuration

## Edge Cases and Error Scenarios

### 1. Network and Connectivity
- **EDGE-001**: No internet connection
- **EDGE-002**: VPN required but not connected
- **EDGE-003**: Authentication failures to git providers
- **EDGE-004**: Remote repository access denied

### 2. Repository State Issues
- **EDGE-005**: Dirty working directory preventing checkout
- **EDGE-006**: Uncommitted changes that would be lost
- **EDGE-007**: Detached HEAD state
- **EDGE-008**: Merge conflicts during pull
- **EDGE-009**: Local commits not pushed to any remote

### 3. Branch and Remote Issues
- **EDGE-010**: Origin/HEAD not set or pointing to non-existent branch
- **EDGE-011**: Trunk branch doesn't exist on origin
- **EDGE-012**: User fork remote exists but points to wrong repository
- **EDGE-013**: Multiple remotes with same provider type
- **EDGE-014**: Remote branch deletion failures due to permissions

### 4. Configuration Issues
- **EDGE-015**: Missing or invalid configuration file
- **EDGE-016**: Configuration conflicts between global and local settings
- **EDGE-017**: Invalid remote patterns in configuration

## Configuration Schema

```yaml
# Global configuration (~/.gcm.yaml)
providers:
  github:
    username: "myuser"
    fork_remote: "myuser"
    vpn_command: null
  gitlab:
    username: "myuser"
    fork_remote: "myuser" 
    vpn_command: "vpn-up"

remotes:
  upstream: "origin"
  
branches:
  trunk_aliases: ["main", "master", "trunk"]

behavior:
  auto_create_forks: false
  confirm_destructive: true
  parallel_operations: true
```

## Non-Functional Requirements

### 1. Performance
- **NFR-001**: Operations should complete within reasonable time (< 30 seconds for typical repos)
- **NFR-002**: Support parallel operations where safe (remote pruning, branch deletion)

### 2. Reliability
- **NFR-003**: Never lose user data without explicit confirmation
- **NFR-004**: Atomic operations where possible (all-or-nothing)
- **NFR-005**: Comprehensive logging for debugging

### 3. Usability
- **NFR-006**: Clear progress indicators for long-running operations
- **NFR-007**: Helpful error messages with suggested remediation
- **NFR-008**: Dry-run mode for previewing changes

### 4. Portability
- **NFR-009**: Work on Linux, macOS, and Windows with git installed
- **NFR-010**: No hardcoded paths or user-specific values
- **NFR-011**: Minimal external dependencies beyond Python standard library and git

## Dependencies

### Required
- Python 3.8+
- Git 2.0+
- Standard shell environment

### Optional
- VPN client (configurable command)
- Git provider CLI tools (gh, glab) for fork creation