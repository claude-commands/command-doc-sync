# command-doc-sync

A Claude Code slash command for keeping documentation in sync with code.

## Installation

```bash
# Clone to your preferred location
git clone git@github.com:claude-commands/command-doc-sync.git <clone-path>/command-doc-sync

# Symlink (use full path to cloned repo)
ln -s <clone-path>/command-doc-sync/doc-sync.md ~/.claude/commands/doc-sync.md
```

## Usage

```
/doc-sync              # Check all documentation
/doc-sync README.md    # Check specific file
/doc-sync --api        # Check API documentation
/doc-sync --fix        # Auto-fix sync issues
```

## What it does

1. Finds all documentation files
2. Parses code examples and references
3. Compares with actual source code
4. Identifies outdated content
5. Generates update suggestions
6. Auto-fixes simple issues (optional)

## Output Format

```markdown
# Documentation Sync Report

## Summary
| Category | OK | Outdated |
|----------|-----|----------|
| README | 3 | 2 |
| API Docs | 1 | 3 |

## Issues Found

### README.md - Outdated Installation
Line 23: Package name changed
- Old: `npm install my-package`
- New: `npm install @org/my-package`

## Auto-Fix Available
- [ ] Package name update
- [ ] Function signature
```

## What Gets Checked

| Type | Checks |
|------|--------|
| README | Install commands, usage examples |
| API Docs | Endpoints, parameters, responses |
| Guides | Code samples, CLI commands |
| Config | Options, defaults, environment vars |

## Requirements

- Git repository with documentation
- Claude Code with Opus 4.5 model access

## Updates

```bash
cd <clone-path>/command-doc-sync && git pull
```
