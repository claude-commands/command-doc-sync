---
argument-hint: "[path-or-scope]"
description: "Keep documentation in sync with code changes"
model: claude-opus-4-5-20251101
allowed-tools: ["Bash", "Read", "Write", "Edit", "Glob", "Grep", "AskUserQuestion"]
---

**If `$ARGUMENTS` is empty or not provided:**

Analyze documentation vs code for sync issues.

**Usage:** `/doc-sync [path-or-scope]`

**Examples:**

- `/doc-sync` - Check all documentation
- `/doc-sync README.md` - Check specific file
- `/doc-sync --api` - Check API documentation
- `/doc-sync --fix` - Auto-fix sync issues

**Workflow:**

1. Find documentation files
2. Compare with source code
3. Identify outdated content
4. Generate update suggestions
5. Apply fixes (optional)

Proceed with full documentation sync check.

---

**If `$ARGUMENTS` is provided:**

Check and sync specified documentation scope.

## Configuration

- **Scope**: `$ARGUMENTS`
  - Path: Check specific doc file
  - `--api`: API documentation
  - `--readme`: README files
  - `--fix`: Auto-apply fixes
  - `--report`: Generate detailed report

## Steps

1. **Find Documentation Files**

   ```bash
   # Find all documentation
   fd -e md -e mdx -e rst -e txt -g "README*" -g "CONTRIBUTING*" -g "CHANGELOG*"

   # API docs
   fd -e yaml -e json -g "openapi*" -g "swagger*"

   # Doc directories
   ls -d docs/ documentation/ wiki/ 2>/dev/null
   ```text

   Categorize:
   - README files
   - API documentation
   - Developer guides
   - Architecture docs
   - Code comments

2. **Parse Documentation Content**

   Extract from docs:
   - Code examples
   - CLI commands
   - API endpoints
   - Configuration options
   - Function signatures
   - Installation steps

3. **Compare with Source Code**

   **Code Examples:**

   ```bash
   # Find code blocks in markdown
   grep -A 20 '```' README.md | grep -v '```'

   # Verify imports exist
   rg "import.*from" --type=ts --type=js
   ```text

   Check:
   - Do imported modules exist?
   - Do function names match?
   - Are signatures correct?

   **CLI Commands:**

   ```bash
   # Extract commands from docs
   grep -E '^\s*\$|npm run|yarn |go |python ' README.md

   # Verify in package.json/Makefile
   cat package.json | jq '.scripts'
   ```text

   **API Endpoints:**
   - Compare OpenAPI spec with actual routes
   - Check response examples match
   - Verify parameters documented

4. **Identify Sync Issues**

   **Types of issues:**

   | Issue | Example | Severity |
   |-------|---------|----------|
   | Missing function | Doc refs removed func | High |
   | Wrong signature | Params changed | High |
   | Outdated example | Old syntax | Medium |
   | Wrong command | npm script renamed | Medium |
   | Typo in path | File moved | Low |

5. **Check README Sections**

   **Installation:**
   - Verify commands work
   - Check dependencies listed
   - Confirm versions accurate

   **Usage Examples:**
   - Do examples run?
   - Are imports correct?
   - Do outputs match?

   **Configuration:**
   - Are all options documented?
   - Are defaults accurate?
   - Are env vars listed?

6. **Check API Documentation**

   ```bash
   # Compare OpenAPI with routes
   rg "router\.(get|post|put|delete)" --type=ts --type=js

   # Extract documented endpoints
   cat openapi.yaml | grep "paths:" -A 1000
   ```text

   Verify:
   - All routes documented
   - Request bodies accurate
   - Response schemas match
   - Error codes listed

7. **Check Code Comments**

   ```bash
   # Find JSDoc/GoDoc comments
   rg "^/\*\*|^// " --type=ts --type=go

   # Find TODO/FIXME in docs
   rg "TODO|FIXME|DEPRECATED" --type=md
   ```text

   Identify:
   - Outdated JSDoc
   - Stale TODOs
   - Deprecated warnings

8. **Generate Sync Report**

   ```markdown
   # Documentation Sync Report

   **Project**: my-app
   **Files Checked**: 12
   **Issues Found**: 7

   ## Summary

   | Category | OK | Outdated | Missing |
   |----------|-----|----------|---------|
   | README | 3 | 2 | 0 |
   | API Docs | 1 | 3 | 1 |
   | Guides | 2 | 1 | 0 |

   ## High Priority Issues

   ### 1. README.md - Outdated Installation
   **Line 23-25**

   Documentation says:
   ```bash
   npm install my-package
   ```text

   But package name changed to:

   ```bash
   npm install @org/my-package
   ```text

   **Fix**: Update package name

   ---

   ### 2. API Docs - Missing Endpoint

   **Missing**: `POST /api/v2/users/batch`

   This endpoint exists in code but not documented:

   ```typescript
   // src/routes/users.ts:45
   router.post('/batch', batchCreateUsers);
   ```text

   **Fix**: Add endpoint documentation

   ---

   ### 3. README.md - Wrong Function Signature

   **Line 67**

   Documentation shows:

   ```typescript
   createUser(name: string): User
   ```text

   Actual signature:

   ```typescript
   createUser(name: string, email: string): Promise<User>
   ```text

   **Fix**: Update function signature

   ---

   ## Medium Priority Issues

   ### 4. Config Example Outdated

   **File**: docs/configuration.md:34

   Option `maxRetries` renamed to `retryCount`

   ### 5. CLI Command Changed

   **File**: README.md:89

   `npm run build:prod` → `npm run build`

   ## Low Priority Issues

   ### 6. Typo in File Path

   **File**: CONTRIBUTING.md:12

   `src/utils/helpers.ts` → `src/lib/helpers.ts`

   ## Auto-Fix Available

   The following issues can be auto-fixed:

   - [ ] Package name update (README.md:23)
   - [ ] Function signature (README.md:67)
   - [ ] Config option name (docs/configuration.md:34)
   - [ ] CLI command (README.md:89)
   - [ ] File path (CONTRIBUTING.md:12)

   Run `/doc-sync --fix` to apply.

   ## Manual Review Required

   - API endpoint documentation (needs content)
   - New features not yet documented

   ```text

9. **Apply Fixes (if --fix)**

   For auto-fixable issues:

   ```bash
   # Update README
   sed -i 's/npm install my-package/npm install @org\/my-package/' README.md
   ```text

   Track changes:
   - Create backup
   - Apply fix
   - Log change
   - Verify result

10. **Generate Missing Documentation**

    For undocumented code:

    ```markdown
    ## Suggested Documentation

    ### New Endpoint: POST /api/v2/users/batch

    Creates multiple users in a single request.

    **Request Body:**
    ```json
    {
      "users": [
        { "name": "Alice", "email": "alice@example.com" },
        { "name": "Bob", "email": "bob@example.com" }
      ]
    }
    ```text

    **Response:**

    ```json
    {
      "created": 2,
      "users": [...]
    }
    ```text

    ```text

## Output Structure

```markdown
# Documentation Sync Report

## Summary
[Issue counts by category]

## High Priority
[Breaking documentation issues]

## Medium Priority
[Outdated but not breaking]

## Low Priority
[Minor inconsistencies]

## Auto-Fix
[Issues that can be auto-corrected]

## Manual Review
[Issues needing human input]
```text

## What Gets Checked

| Doc Type | Checks |
|----------|--------|
| README | Install, usage, examples |
| API Docs | Endpoints, params, responses |
| Guides | Code samples, commands |
| Config | Options, defaults, types |
| Comments | JSDoc, GoDoc accuracy |

## Notes

- Run after major refactors
- Include in CI for detection
- Auto-fix is conservative
- Review generated docs before committing
- Keep a changelog for doc changes
